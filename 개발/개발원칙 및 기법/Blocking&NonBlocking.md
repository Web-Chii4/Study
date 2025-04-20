# 블로킹I/O와 논블로킹I/O

## 목표

아래의 문항에 대해서 대답할 수 있어야 한다.

1. sync와 async의 차이와 async가 문제를 해결하는 방식을 이야기 해주세요.
2. blocking + sync / blocking + async / non-blocking + async / non-blocking + sync 등 각 조합이 동작하는 방식과 특징을 설명해주세요.
3. nodejs와 mysql 같은 rdb를 사용할 때는 3번의 사례중 무엇에 해당할까요?
4. nodejs와 mongodb 같은 no-sql을 사용할 때는 3번의 사례중 무엇에 해당할까요?
5. nodejs와 궁합이 좋은 데이터베이스는 무엇이고 이유가 뭘까요?

## 블로킹 I/O란?

블로킹 I/O를 이해하기 전 I/O에 대해 이해하고 넘어가야 한다.

**I/O란?**

> I/O는 Input(입력)과 Output(출력)을 의미한다. 여기서 I/O는 **네트워크 입출력, 파일 입출력, 장치 입출력, Pipe 입출력 등**이 있다.
>

Tip)

Pipe는 프로세스 간 데이터를 전달하기 위한 통신 방식으로, 한 프로세스의 출력 데이터를 다른 프로세스의 입력 데이터로 전달할 때 사용된다.

![Image](https://github.com/user-attachments/assets/8595b06b-5a81-49f1-9c8f-7a7ce95b701b)


그렇다면 블로킹 I/O는?

> **블로킹 I/O**는 스레드가 작업을 처리하는 과정에서 I/O 작업을 만나면, 해당 **I/O 작업이 끝날 때까지 Thread가 대기하는 방식**을 의미한다.
>

위의 그림에 따른 과정을 설명하자면

1. 스레드가 동작하다가 네트워크 작업을 OS에 요청한다.
2. 스레드는 이후 동작을 처리하지 않고 I/O의 완료 신호가 올 때까지 기다린다.
3. OS의 Kernel에서는 해당 네트워크 요청을 처리한다.
4. OS의 Kernel에서는 네트워크 요청에 대한 응답을 받는다.
5. 받은 응답을 프로세스에게 반환한다.

위와 같이 스레드는 I/O작업이 완료되기 전까지 이후 작업을 처리하지 않는다.

### 특징

스레드가 잠시 대기하기 때문에 논블로킹 I/O는 다음과 같은 특성을 가지고 있다.

**장점)**

- **작업의 순서과 직관적이기 때문에 구현이 간단**하다.
- 순서가 보장되기 때문에 **스레드 안정성이 높다**(Thread Safe 하다).

**단점)**

- I/O 작업 처리 대기시간 동안 **스레드가 낭비**된다.
- **동시 처리(Concurrency)에 비효율적**이다.

## 논블로킹 I/O란?

> **논블로킹 I/O**란 스레드가 작업을 처리하는 과정에서 I/O작업을 만나면 해당 작업을 요청한 후 **I/O작업이 끝나는 것을 기다리지 않고 다음 작업을 처리하는 방식**을 의미한다.
>

![Image](https://github.com/user-attachments/assets/da8e2227-9d67-40f4-9bd9-2f4062444ba4)

위의 그림에서 볼 수 있듯이 스레드에 의해 명령이 처리되다가 I/O명령을 만나게 되면, I/O작업을 요청하고 이후의 명령을 처리한다.

I/O작업 이후, 후속 처리가 반드시 있어야 한다면, (ex. 외부 API요청 후 응답을 통해 UI작업을 한다.) 후속 처리를 위한 **콜백 함수**를 등록한다. → 이를 통해 **선언적 프로그래밍**이라고 볼 수 있다.

### 특징

**장점)**

- 스레드의 **CPU사용률이 높다**.
- **동시 처리(Concurrency)에 효율적**이다.
- 오래 걸리는 I/O작업의 경우 스레드가 대기하지 않기 때문에

**단점)**

- 비동기적으로 돌아감으로써 **스레드 안정성이 떨어진다**.
- **가독성이 떨어진다**.

## 무조건 논블로킹이 좋은가?

위의 특징에서 볼 수 있듯이 논블로킹이 스레드의 사용률을 높여준다. 성능적으로 큰 이점이 있는 논블로킹을 항상 사용하는 것이 좋지 않을까?

상황에 따라 다른 것 같다.

### 1. I/O작업 시간이 오래 걸리는 경우

I/O작업이 오래 걸리는 경우는 오래 걸리는 시간만큼 스레드가 대기하기 때문에 **스레드 사용률을 위해** **논블로킹**이 더 유리하다.

### 2. I/O 작업 시간이 짧은 경우

I/O작업 시간이 짧은 경우 블로킹으로 동작하더라도 프로그램 동작에 있어 큰 차이가 없다면, 오히려 **가독성의 측면에 있어** **블로킹이** 더 좋다.

논블로킹 방식)

```java
public Mono<ContainerInfo> assignUserContainer() {
    return this.dockerAPI.createUserContainerAPI(newContainerBody)
            .flatMap(containerId ->
                    this.dockerAPI.startUserContainerAPI(containerId)
                            .then(this.dockerAPI.getContainerPortAPI(containerId)
                                    .map(containerPort -> new ContainerInfo(containerId, containerPort)))
            );
}
```

블로킹 방식)

```ts
async assignContainer(ipAddress: string) {
    try {
		    const containerId = await this.createContainer();
		    let containerPort;
        await this.startContainer(containerId);
        containerPort = await this.getContainerPort(containerId);
        
		    return {
			    containerId,
			    containerPort
		    };
    } catch (startError) {
        try {
            await this.deleteContainer(containerId);
        } catch (deleteError) {
            this.logger.error(deleteError);
        }
        throw startError;
    }
    return null;
}
```

가독성 측면으로 보았을 때 블로킹 방식이 동작 순서에 따라 정의했기 때문에 가독성이 훨씬 좋다는 것을 볼 수 있다.

## 비동기와 동기

![Image](https://github.com/user-attachments/assets/f0d10ca5-52b3-40ed-821d-dbaa7c504a38)

### 비동기(Asynchronouse)

> 코드가 순차적으로 실행되는 것이 아니라, 병렬적으로 처리하는 방식이다.
>

즉, 코드의 실행 순서는 보장되지 않는다.

### 동기적(Synchronous)

> 코드가 순차적으로 실행되는 방식이다.
>

코드의 실행 순서가 보장된다.

### 논블로킹I/O와 비동기, 블로킹I/O와 동기의 관계

논블로킹은 비동기적이다. 블로킹은 동기적이다. 라는 말을 들어본 적 있을 것이다. 이는 블로킹 I/O는 동기적인 방식으로 돌아가고, 논블로킹 I/O는 비동기적인 방식으로 돌아가기 때문이다.

기본적으로 두 방식 모두 다른 장치에 의해 돌아간다.

1. CPU(스레드)
2. I/O(네트워크, 파일 등)

다른 장치에 의해 병렬적으로 수행되며 I/O작업의 완료는 언제 끝날지 그 누구도 모른다.(I/O명령을 수행하는 장치의 환경에 따라 달라지기 때문)

따라서, 논블로킹 I/O는 순서가 보장되지 않는 비동기의 특성을 지니고, 블로킹 I/O는 순서가 보장되는 동기의 특성을 지니게 되는 것이다.

### 멀티 스레드의 경우

그렇다면 멀티 스레드 경우는?

만약 스레드 2개로 하나의 작업을 처리하는 경우는 어떻게 구현하느냐에 따라 동기, 비동기로 나뉠 수 있다.

1. 하나의 스레드에서 다른 스레드에 작업을 요청하고 기다린다(동기)
2. 하나의 스레드에서 다른 스레드에 작업을 요청하고 다음 명령을 실행한다.(비동기)

해당 사례의 경우는 I/O와 작업을 수행하는 장치만 달라졌을 뿐이다.

1. 스레드 1 (CPU)
2. 스레드 2 (I/O 장치)

### 결론

논블로킹 I/O는 비동기(Asynchronous) 처리 방식 중 하나이고, 블로킹 I/O 또한 동기(Synchronous) 처리 방식 중 하나이다.

논블로킹 I/O와 블로킹 I/O는 I/O작업에 대한 처리 방식이고, **이벤트 처리**, **연산** 등에도 적용이 가능하다.

### sync와 async의 차이와 async가 문제를 해결하는 방식을 이야기 해주세요.

동기 방식은 모든 작업에 대해 실행과 완료 작업이 순차적으로 실행되는 방식인 반면, 비동기 방식은 모든 작업의 완료를 기다리지 않고, 다른 작업을 수행합니다. 따라서, 동기는 순차적으로 동작을 수행함에 따라 코드의 예측이 쉽고 이에 따라 디버깅이 용이합니다. 반면, 비동기 방식은 다른 작업의 완료를 기다리지 않고 다음 동작을 수행하기 때문에 효율적으로 리소스를 활용할 수 있습니다.

### blocking + sync / blocking + async / non-blocking + async / non-blocking + sync 등 각 조합이 동작하는 방식과 특징을 설명해주세요.

1. nodejs와 mysql 같은 rdb를 사용할 때는 3번의 사례중 무엇에 해당할까요?
2. nodejs와 mongodb 같은 no-sql을 사용할 때는 3번의 사례중 무엇에 해당할까요?
3. nodejs와 궁합이 좋은 데이터베이스는 무엇이고 이유가 뭘까요?