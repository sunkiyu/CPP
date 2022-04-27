## ABA Problem
```cpp
 class ABAStack
 {
       volatile Obj* m_topPtr;
       
       Obj* Pop()
       {
           while(1)
           {
               Obj* oldTopPtr = m_topPtr;                                                              // (1)
               if (!oldTopPtr) return NULL;                                                            // (2)
               Obj* nextPtr = oldTopPtr->next;                                                         // (3)
               if (CompareAndExchange( m_topPtr, oldTopPtr, nextPtr))                                  // (4) 
                   return oldTopPtr;
           }
       } 
       
       void Push(...){...};
 }
```
* 현재 스택에는   
* |0x60|   
* |0x50|   
* |0x40|   
* |0x30|   
* 이 순서로 데이터가 쌓여있다.   
* Thread 1,2,3 가 있다.   
* Thread 1이 위의 (3)번 과정까지 실행했다고 치자.
* m_topPtr은 0x60을 가르킬 것이고 oldTopPtr 또한 (1)에 의해 0x60을 가르킬 것이다.   
* 그리고 nextPtr은 (3)에 의해 0x50을 가르킬 것이다.
* 이때 스레드 2가 Pop을 한번한다.
* 스레드 2도 마찬가지로 m_topPtr은 0x60, oldTopPtr은 0x60, nextPtr은 0x50을 가르키고 있을 것이다.
* CAS 연산이 실행되면(4), m_topPtr과 oldTopPtr이 0x60으로 같으므로 m_topPtr = 0x50이 될것이고 true를 반환한다.
* 또 한번 스레드 3가 Pop을 한다.
* 스레드 3의 초기값은 m_topPtr = 0x50, oldTopPtr = 0x50, nextPtr = 0x40이 될것이고,
* CAS 연산이 실행되면(4) m_topPtr == oldTopPtr == 0x50이므로 m_topPtr = 0x40이 될것이고 true를 반환한다.
* 이 때, 두 번의 Pop에 의해 반환된 0x60,0x50 주소는 메모리 매니저에 의해 다른 리소스 자원이 할당 되었을 수 있다. 한마디로 현재 프로세스에서는 당장 유효한 메모리 주소가 아니다.   
* 이 때, Thread3이 Push를 해서 m_topPtr = 0x60으로 메모리를 할당 받았다.
* 이 때, Thread1이 (4)번 CAS 연산을 하게되면 m_topPtr == oldTopPtr == 0x60이 되므로 Thread1의 (3) 실행과정에 의해 nextPtr이 0x50이 되었으므로
* m_TopPtr = 0x50을 가르킬 것이다. 
* 그러나 위에도 설명했든 0x50 번지 주소는 이미 두번의 Pop 과정에 의해 반환되었던 메모리 중 하나이기 때문에 메모리 매니저에 의해 관리되고 있는, 현재 프로세스상 유효하지 않은 주소이다. 
* 하지만, 멀티스레드 환경의 CAS 연산에 의해 유효하지 않은 주소 0x50을 가르키고 있기 때문에 ABA 문제가 발생한다.   
### 즉, 멀티스레드 환경에서 해당 예제는 값 자체가 아닌 단순 주소값을 비교하였다   
### 다른 스레드에 의해 TopNode의 NextNode주소가 이미 반환된 메모리 주소가 되어 변경사항이 발생했음에도 불구하고   
### TopNode의 주소값이 일치한다는 이유만으로 반환된 NextNode 주소값을 할당시켜 발생하는 문제이다.


### Stack Header에 Next의 주소값 뿐만 아니라 Sequence와 Depth를 두어 새로들어온 Stack인지 기존에 있던 Stack인지 더 정교하게 검사할 수 있다.   
### 기존 단순 주소값만 비교할 경우 새롭게 들어온 Stack일지라도 주소값이 같다면 기존에 있던 Stack과 똑같이 처리하였지만,   
### Sequence와 Depth도 함께 비교한다면 주소값이 같더라도 Pop & Push 작업에 의해 Sequence와 Depth 값이 변경되었을 것이므로 기존의 Top과 다른것을 알 수 있다.   

* MS에서 제공하는 SLIST_ENTRY 구조체가 ABA Problem 대책을 제공한다.    
* 해당 구조체는 16바이트 정렬을 시키고 각 스택마다 Sequence와 Depth를 둬서 CAS 연산을 진행한다.   
