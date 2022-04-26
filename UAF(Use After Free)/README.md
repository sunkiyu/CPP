# User After Free

![image](https://user-images.githubusercontent.com/68372094/165222739-86514642-d755-40ae-81a7-7ab30c7b1799.png)   
* malloc 하였을 경우 usf는 0x008e5be8메모리 주소공간을 가르킨다.   
![image](https://user-images.githubusercontent.com/68372094/165222930-2f890623-8fd4-4ea3-8e1f-d519a5ea96c6.png)   
* usf 메모리 공간을 free 한뒤에 같은 크기(50Byte)만큼 usf2를 할당 받으니 같은 주소로 할당되었다.
* 이러한 힙 영역 할당 특성은 취약점을 띈다.
