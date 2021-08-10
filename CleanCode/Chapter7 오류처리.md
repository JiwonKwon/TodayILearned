# Chapter7. 오류처리

상당수 코드의 기반은 오류 처리 코드에 좌우되기 때문에 코드와 연관성이 있다.

---

## 오류코드보다 예의를 사용하라

오류 플래그, 호출자에게 오류 코드를 return하는 방법보다 예의를 던져 try catch 하는 것이 낫다.

example1) → 논리와 오류처리코드가 섞이지 않아 코드가 깔끔해진다.

```java
public class DeviceController {
	...
	public void sendShutDown() {
		try {
			tryToShutDown();
		} catch (DeviceShutDownError e) {
			logger.log(e);
		}
	}

	private void tryToShutDown() throws DeviceShutDownError {
		DeviceHandle handle = getHandle(DEV1);
		DeviceRecord record = retrieveDeviceRecord(handle);
		pauseDevice(handle); 
		clearDeviceWorkQueue(handle); 
		closeDevice(handle);
	}

	private DeviceHandle getHandle(DeviceID id) {
		...
		throw new DeviceShutDownError("Invalid handle for: " + id.toString());
		...
	}
	...
}
```

## Try-Catch-Finally 문부터 작성하라

try문은 transaction처럼 동작하는 실행코드로, catch문은 try문에 관계없이 프로그램을 일관적인 상태로 유지하도록 한다.

## 미확인 Unchecked Exceptions를 사용하라

*미확인 예외란?*

*checked 예외* 는 컴파일 단계에서 확인되며 반드시 처리해야 하는 예외

- IOException
- SQLException

*Unchecked 예외* 는 실행 단계에서 확인되며 명시적인 처리를 강제하지는 않는 예외

- NullPointerException
- IllegalArgumentException
- IndexOutOfBoundException
- SystemException

안정적 소프트웨어를 제작하는 요소로 확인된(checked) 예외가 반드시 필요하지는 않다.

아주 중요한 라이브러리를 작성한다면 모를까 일반적인 애플리케이션은 의존성이라는 비용이 이익보다 크다.

## 예외에 의미를 제공하라

전후상황을 충분히 덧붙이고 오류메시지에 정보를 담아 예외와 함께 던진다.

example2) 외부 라이브러리 호출 시 모든 예외를 호출자가 잡아내고 있다.

```java
ACMEPort port = new ACMEPort(12);

 try {
     port.open();
 } catch (DeviceResponseException e) {
     reportPortError(e);
     logger.log("Device response exception", e);
 } catch (ATM1212UnlockedException e) {
     reportPortError(e);
     logger.log("Unlock exception", e);
 } catch (GMXError e) {
     reportPortError(e);
     logger.log("Device response exception");
 } finally {
     ...
 }
```

example3) 감싸기 기법. 간결하게 예외에 대응함

```java
LocalPort port = new LocalPort(12);
 try {
     port.open();
 } catch (PortDeviceFailure e) {
     reportError(e);
     logger.log(e.getMessage(), e);
 } finally {
     ...
 }

public class LocalPort {
     private ACMEPort innerPort;

     public LocalPort(int portNumber) {
         innerPort = new ACMEPort(portNumber);
     }

     public void open() {
         try {
             innerPort.open();
         } catch (DeviceResponseException e) {
             throw new PortDeviceFailure(e);
         } catch (ATM1212UnlockedException e) {
             throw new PortDeviceFailure(e);
         } catch (GMXError e) {
             throw new PortDeviceFailure(e);
         }
     }
     ...
 }
```

외부 API를 감싸면 아래와 같은 장점이 있다.

- 에러 처리가 간결해짐
- 외부 라이브러리와 프로그램 사이의 의존성이 크게 줄어듦
- 프로그램 테스트가 쉬워짐
- 외부 API 설계 방식에 의존하지 않아도 됨

---

## 정상 흐름을 정의하라