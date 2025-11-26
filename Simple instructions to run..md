### Run server

server1  <img width="344" height="20" alt="image" src="https://github.com/user-attachments/assets/254d7f3a-4d7a-49d8-b5a3-657917166566" />

server2  <img width="344" height="20" alt="image" src="https://github.com/user-attachments/assets/6624ab59-2ca2-4389-90c1-5a2a5bc23c84" />

#### output

server1  <img width="318" height="17" alt="image" src="https://github.com/user-attachments/assets/2f263db2-397b-49d4-9509-3abcef4032ee" />

server2  <img width="318" height="17" alt="image" src="https://github.com/user-attachments/assets/ad830260-edb2-4ac2-a0dd-80371a5e2af7" />

### Run proxy
<img width="304" height="18" alt="image" src="https://github.com/user-attachments/assets/949477e7-2919-4e0f-9537-7c09cf79813c" />

#### output
<img width="285" height="21" alt="image" src="https://github.com/user-attachments/assets/c314c44b-f46a-45f1-9cfd-a23055436ef7" />

### Run client

client1  <img width="338" height="22" alt="image" src="https://github.com/user-attachments/assets/507c4e8e-ad65-483c-b3d1-29db4365e46b" />

client2  <img width="341" height="22" alt="image" src="https://github.com/user-attachments/assets/b6d5ea00-b9d7-4db7-88b3-d22475773cfa" />

#### output

client1  <img width="429" height="23" alt="image" src="https://github.com/user-attachments/assets/fdf1363d-bf72-4fa7-b62d-6d4f84226ee1" />

client2  <img width="419" height="19" alt="image" src="https://github.com/user-attachments/assets/f73e8380-7119-4cb9-b41e-55aaf00128dd" />

proxy連到兩個client

<img width="450" height="42" alt="image" src="https://github.com/user-attachments/assets/dc01259d-5067-4514-a2a5-e212d41ed76e" />

### result

Client1 有輪流傳到server1 跟 server2 並收到回傳

<img width="439" height="153" alt="image" src="https://github.com/user-attachments/assets/8d56540d-6c6b-4492-ad36-4d8344c67773" />

Client2 有輪流傳到server1 跟 server2 並收到回傳

<img width="439" height="155" alt="image" src="https://github.com/user-attachments/assets/1e0f57b4-06aa-4426-8e0b-f59d5f703544" />

proxy可以能傳資料，client也可以輪流傳不需照順序也不會亂

<img width="533" height="149" alt="image" src="https://github.com/user-attachments/assets/559e8152-0daa-4a3a-8959-8881dda8fa86" />

Server1 收到資料回傳

<img width="459" height="150" alt="image" src="https://github.com/user-attachments/assets/eab56623-bb7b-402e-aef6-96b3951e3422" />

Server2 收到資料回傳

<img width="462" height="153" alt="image" src="https://github.com/user-attachments/assets/b4aafa8c-1925-44a5-957c-57b6a218d81a" />


### round-robin 的核心程式
```
next_index = (next_index + 1) % len(SERVERS)
```
