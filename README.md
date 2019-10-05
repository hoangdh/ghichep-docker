## Docker là gì?

### Nội dung bài viết

- [1. Khái niệm Docker](#1)

- [2.Cấu trúc và thành phần của Docker](#2)

- [3. Network trong Docker](#3)

- [4. Volume trong Docker](#4)

- [5. Dockerfile](#5)
  - [Chia sẻ một số Dockerfile](#5.1)
  
- [6. Docker Compose](#6)

### 1. Khái niệm <a name="1"></a>

Docker là một nền tảng dành cho các developer và sysadmin để **phát triển, triển khai và chạy** các ứng dụng bằng các **container**. Việc đóng gói thành container này giúp cho việc triển khai các ứng dụng trở nên dễ dàng hơn.

Công nghệ container ngày càng phổ biến bởi:
- Linh hoạt: Có thể đóng gói từ ứng dụng đơn giản đến phức tạp
- Nhỏ gọn: Các container tận dụng, sử dụng chung tài nguyên; kernel của host. Có thể chạy ở mọi nơi, mọi nền tảng.
- Khả năng thay đổi linh hoạt: Cập nhật và nâng cấp nhanh chóng.
- Khả năng mở rộng: Dễ dàng tăng và phân tán tự động các container
- Phân tầng dịch vụ: Mỗi dịch vụ khi deploy sẽ được phân tầng, nằm trên các dịch vụ đang có sẵn. Như vậy sẽ không làm ảnh hưởng tới dịch vụ đang chạy.

#### Khái niệm containers và images

- Một container được khởi chạy từ image. Như vậy, image là một gói thực thi chứa bên trong là tất cả những gì cần thiết, liên quan để chạy ứng dụng như mã nguồn, các thư viện, runtime, các biến môi trường và các file cấu hình liên quan.
- Một container là một instance đang chạy được khởi tạo từ image.

#### So sánh giữa VM với container

- Container chạy trực tiếp trên môi trường máy chủ như một tiến trình và chia sẻ phần kernel bên dưới dùng chung với máy chủ chứa nó
- VM tạo ra một môi trường giả lập hoàn toàn tách biệt như 1 máy hoàn chỉnh thông qua việc phân bổ tài nguyên của máy chủ, do đó sẽ tốn tài nguyên nhiều hơn cho hệ điều hành của máy ảo

<img width="100%"  src="https://i2.wp.com/blog.docker.com/wp-content/uploads/Blog.-Are-containers-..VM-Image-1-1024x435.png?ssl=1" />

### 2. Cấu trúc và thành phần của Docker <a name="2"></a>

Docker bao gồm:

- Docker Client: Giao diện để tương tác giữa người dùng với Docker Daemon - HOST
- Docker Daemon (HOST): Lưu trữ image local và khởi chạy container từ những image đó
- Docker Hub (registry): Nơi lưu trữ các images 


<img width='75%' src="https://cdn-images-1.medium.com/max/800/0*XKTCmmou4fT6os-c." />

#### 2.1 Docker Client

Docker client dùng để tương tác giữa người dùng và Docker Daemon,  Daemon sẽ biên dịch và thực thi các câu lệnh đã tương tác qua Docker client.

##### Xem phiên bản đang sử dụng

> docker version

Câu lệnh sẽ trả về phiên bản của Docker client và Daemon (server)

```
Client:
 Version:           18.09.6
 API version:       1.39
 Go version:        go1.10.8
 Git commit:        481bc77156
 Built:             Sat May  4 02:34:58 2019
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          18.09.6
  API version:      1.39 (minimum version 1.12)
  Go version:       go1.10.8
  Git commit:       481bc77
  Built:            Sat May  4 02:02:43 2019
  OS/Arch:          linux/amd64
  Experimental:     false
```

#### Xem thông tin chi tiết về Docker

Cung cấp thông tin về tài nguyên máy chủ, chi tiết về Docker.

> docker info

```
Containers: 3
 Running: 3
 Paused: 0
 Stopped: 0
Images: 7
Server Version: 18.09.6
Storage Driver: overlay2
 Backing Filesystem: xfs
 Supports d_type: true
 Native Overlay Diff: true
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins:
 Volume: local
 Network: bridge host macvlan null overlay
 Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
Swarm: inactive
Runtimes: runc
Default Runtime: runc
Init Binary: docker-init
containerd version: bb71b10fd8f58240ca47fbb579b9d1028eea7c84
runc version: 2b18fe1d885ee5083ef9f0838fee39b62d653e30
init version: fec3683
Security Options:
 seccomp
  Profile: default
Kernel Version: 3.10.0-957.10.1.el7.x86_64
Operating System: CentOS Linux 7 (Core)
OSType: linux
Architecture: x86_64
CPUs: 1
Total Memory: 991.2MiB
Name: minion4
ID: 6BBX:UEEB:SDZ4:LGES:HSFN:WIEY:TASR:5MRK:JMV6:7O7W:CTGY:ATPX
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): false
Registry: https://index.docker.io/v1/
Labels:
Experimental: false
Insecure Registries:
 127.0.0.0/8
Live Restore Enabled: false
Product License: Community Engine
```

#### Tìm kiếm image trên registry

> docker search python

![a](https://docs.admicro.vn/uploads/images/2019/05/09/nPHvhj6FzEN3ALhKYDkxd3awcOQXLIAQ.png "search")

- NAME: Tên của Image
- DESCRIPTION: Mô tả về image
- STARS: Số lượt rating. Số này càng cao thì chất lượng của Image càng tốt.
- OFFICIAL: Được cung cấp bởi Hãng hoặc Tổ chức đã được xác nhận trên Registry

#### Chạy một container từ Image 

> docker run -i -t --name web1 ubuntu

Khi chạy **docker pull**, mặc định sẽ tìm trên localhost xem có image nào trùng với yêu cầu trong câu lệnh hay không. Nếu image không có sẵn trên localhost, Docker sẽ tìm kiếm và tải về (pull) từ Registry mặc định.

- `-i`: Vào chế độ tương tác trực tiếp với Container
- `-t`: Hiển thị tty
- `--name`: Đặt tên cho container. Mặc định, nếu không đặt thì sẽ có tên ngẫu nhiên.

Để thoát ra khỏi chế độ tương tác, thao tác lần lượt `CTRL` + `P` sau đó `CTRL` + `Q`

#### Khởi động container ở chế độ chạy nền

> docker run -d  --name web1 ubuntu

#### Liệt kê các container

> docker ps  -a

![PS](https://docs.admicro.vn/uploads/images/2019/05/09/FfJkeCZgd7w3SQpuRd3amnPsJ9obru56.png "PS")

`-a` hoặc `--all`: Hiển thị toàn bộ số container có trên hệ thống
- `CONTAINER ID`: ID của container
- `IMAGE`: Tên của Image khởi tạo 
- `COMMAND`: Câu lệnh chính khi khởi động của container/image
- `CREATE`: Thời gian container được tạo
- `STATUS`: Trạng thái của container
- `PORT`: Cổng của container được ánh xạ với host (HOST:CONTAINER)
- `NAMES`: Tên của container

#### Liệt kê các image

> docker images

Hoặc 

> docker image list

![Docker Images](https://docs.admicro.vn/uploads/images/2019/05/09/5H3iM0Awh7q8NzzTujpICp1puG9hTqpC.png "Docker Images")

- `REPOSITORY`: Tên của image
- `TAG`: Phiên bản của image

#### Dừng hoạt động của container

> docker stop web1

#### Khởi động của container

> docker start web1

Có thể thêm `-i` để có thể tương tác trực tiếp với container.

#### Tương tác với Container đang hoạt động

> docker attach web1

Hoặc tương tác sử dụng môi trường `/bin/bash`

> docker exec -it web1 /bin/bash

#### Xóa container

Container chỉ bị xóa khi ở trạng thái dừng hoạt động.

> docker rm web1

#### Tạo container ánh xạ port với HOST (Máy chủ)

##### Ánh xạ port ngẫu nhiên với HOST

> docker run -d --name db4 -P -e MYSQL_ROOT_PASSWORD=mysql-1 mysql

`-P`: Ánh xạ ngẫu nhiên
`-e`: Gán biến môi trường của trong container

Kiểm tra các port ánh xạ của container

```
> docker port db4
3306/tcp -> 0.0.0.0:32777
33060/tcp -> 0.0.0.0:32776
```

##### Chỉ định port ánh xạ giữa container với HOST

> docker run -d --name db5 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=mysql-1 mysql


Kiểm tra các port ánh xạ của container

```
> docker port db5
3306/tcp -> 0.0.0.0:3306
```

##### Ánh xạ một thư mục trên HOST vào bên trong container

Để ánh xạ, tùy chọn `-v` được sử dụng khi khởi tạo container. Ví dụ, ánh xạ thư mục lưu trữ dữ liệu của mysql trong container với một thư mục trên host.

```
mkdir -p /opt/mysql_data
docker run -d -v /opt/mysql_data:/var/lib/mysql  --name db6 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=mysql@123 mysql
```

##### Liên kết giữa các container với nhau

Việc liên kết các container để chúng có thể 'trò chuyện' với nhau thường được dùng bằng cách expose các port, điều này vô cùng bất lợi. Docker hỗ trợ liên kết các container với nhau bằng cách sử dụng tham số `--link` khi khởi tạo một container mới. Ví dụ, tạo một container khác để chạy ứng dụng webserver sử dụng database là container `db6` vừa tạo ở bước trên:

```
docker run -d --link db6:db.example.com --name wp1 -p 80:80,443:443 wordpress
```

- `--link db6:db.example.com`: Liên kết với container có tên là `db6` và có alias là `db.example.com` (giá trị này tương đương với FDQN)

#### To be continued

#### 2.2 Docker Hub (Registry)

Docker Hub hay thường được gọi là Registry, nơi lưu trữ các image được cộng đồng hoặc các nhà phát triển đóng góp và cung cấp miễn phí, chúng ta có thể tìm các bản images tại đây. Điều này vô cùng tiện lợi, chúng ta chỉ cần pull (tải xuống) các image phục vụ cho nhu cầu ở mọi lúc mọi nơi.

Chúng ta cũng có thể dễ dàng tạo ra những bản image của riêng mình phục vụ cho việc học tập, nghiên cứu, làm việc và chia sẻ chúng cho cộng đồng. Các bước để thực hiện được điều đó như sau:

- Đăng kí một tài khoản tại https://hub.docker.com
- Đưa image của lên Hub

##### Các bước đưa (push) lên Hub

Ở ví dụ này, ta sẽ tạo một image và tạo một vài file trong đó rồi đóng gói lại thành một image.

- **Bước 1**: Sử dụng một image có sẵn nhỏ gọn là `alpine`

> docker run -it --name myimage1 alpine

- **Bước 2**: Sau khi vào chế độ tương tác của container, tạo vài file bất kỳ và thoát khỏi container

```
touch a b c d e f
ls
exit
```

- **Bước 3**: Lưu lại image tại Host local (commit)

> docker commit myimage1 username/myimage1

`username`: Tên đăng nhập Hub
`myimage1`: Tên image muốn lưu trên Hub. Có thể kèm theo Tag. VD: `myimage1:latest` để đánh dấu image bản mới nhất.

- **Bước 4**: Đăng nhập vào Docker Hub

> docker login

Nhập user/password để đăng nhập

- **Bước 5**: Đưa image lên Hub (push)

> docker push username/myimage1

### 3. Network trong Docker <a name="3"></a>

Một trong những điểm mạnh của Docker đó là khả năng kết nối các container để chúng có thể giao tiếp được với nhau và có thể kết nối các container chạy  trên các nền tảng khác nhau. (Linux, Windows,...)

Để liệt kê danh sách Network ở Docker, ta sử dụng câu lệnh sau:

> docker network ls

Để xem các thông tin chi tiết của một Network nào đó, sử dụng câu lệnh:

> docker network inspect <Network-name>

#### Các loại Network trong Docker

Sau khi cài đặt, Docker sẽ tạo mặc định 3 network: `brigde`, `none` và `host`. Khi khởi tạo container, ta có thể chỉ định cho chúng sử dụng network nào thông qua tùy chọn `--network`.

- `brigde`: Network driver mặc định. Mặc định sử dụng `brigde` khi không khai báo trong lúc khởi tạo container. Nó cho phép các container riêng biệt, đơn lẻ (standalone) có thể giao tiếp được với nhau. Dải IP mặc định được cấp cho `brigde` là 172.17.0.0/16
- `host`: Sử dụng thông tin về Network của host cho container. Được hỗ trợ từ phiên bản Docker 17.06 trở lên.
- `none`: Không sử dụng Network trong container.
- `overlay`: là một network driver cho phép kết nối các container nằm trên các Docker Host khác nhau, kích hoạt Swarm sử dụng để truyền thông. Các `overlay` network làm đơn giản hóa việc truyền thông giữa dịch vụ Swarm với container riêng biệt, đơn lẻ hay việc truyền thông giữa 2 container riêng lẻ nằm trên các Docker deamon (Host) khác nhau. Cách này sẽ xóa bỏ việc phải định tuyến (routing) phức tạp ở mức OS giữa các container với nhau.

| Giao thức | Cổng | Mục đích |
| -- | -- | -- |
|  TCP | 4789 | Truyền dữ liệu (data) |
|  TCP/UDP | 7946 | Điều khiển (control) |

- `macvlan`: Sử dụng để gán địa chỉ MAC cho container với mục đích là cho container có vai trò như một thiết bị vật lý. Docker daemon sẽ định tuyến luồng gói tin tới các container thông qua các địa chỉ MAC này. Cách này vô cùng hữu hiệu cho các ứng dụng cũ /lỗi thời (legacy app) các kết nối sẽ được chuyển trực tiếp tới lớp Vật lý thay vì phải định tuyến thông qua lớp mạng bên trong Docker.

Tóm tắt về Network trong Docker

- **User-defined bridge networks**: là sự lựa chọn tốt nhất khi muốn các container trên cùng một host có thể giao tiếp với nhau. (Bridge driver)
- **Host networks**: được sử dụng trong trường hợp muốn sử dụng chung các thông tin về Network trong container nhưng tách biệt các thông tin khác.
- **Overlay network**: Trong trường hợp muốn các container trên các host khác nhau có thể truyền thông qua lại hay muốn nhiều container làm việc cùng nhau khi sử dụng Swarm thì đây là một sự lựa chọn. 
- **MACVlan network**: Sử dụng trong trường hợp muốn container có thể giao tiếp như một thiết bị vật lý, mỗi container sẽ có một địa chỉ MAC duy nhất, không trùng lặp.

### 4. Volume trong Docker <a name="4"></a>

*Docker volumes* vô cùng hữu ích với 2 TH muốn lưu trữ và chia sẻ dữ liệu của các container. Điều này vô cùng quan trọng, khi một container bị xóa bỏ có nghĩa rằng mọi dữ liệu bên trong chúng cũng đều bị 'bốc hơi'. Do vậy, để dữ liệu đó không bị mất thì Docker Volume là một tính năng hữu hiệu.

Để sử dụng Docker volume, khi khởi chạy một container hãy thêm tùy chọn `-v` vào sau câu lệnh `docker run`.

#### Khi nào cần sử dụng Volume?

Giả sử, ta có một container làm web server. Khi đó ta sẽ ánh xạ một thư mục trên host để chứa mã nguồn với thư mục `/var/www/html` (thư mục chứa mã nguồn mặc định. Điều này vô cùng hữu ích khi ta muốn cập nhật mã nguồn (thêm xóa dữ liệu) của trang web; thay vì phải vào bên trong container để thao tác, ta có thể thao tác trực tiếp ở thư mục được ánh xạ trên host.

#### Các loại Volume trong Docker

Docker hỗ trợ 3 kiểu Volume như sau:

- Bind mount
- Volume
- tmpfs mount

<img src="https://i1.wp.com/stephenafamo.com/blog/wp-content/uploads/2018/02/types-of-mounts-volume.png?w=502&ssl=1" />

#### Chi tiết 

 ##### Bind mount 
- **Bind mount**: là một kỹ thuật để ánh xạ trực tiếp một thư mục trên host với một thư mục cụ thể nào đó bên trong container. Khi container bị xóa, dữ liệu bên trong thư mục sẽ không bị ảnh hưởng.

##### Volume
- **Volume**: giống với `bind mount` nhưng thư mục ánh xạ sẽ được quản lý bởi Docker. Thư mục chứa mặc dịnh tại `/var/lib/docker/volumes/`

##### tmpfs mount
- **tmpfs mounts** được sử dụng trong các trường hợp ta không muốn dữ liệu tồn tại trên Docker host hay containers vì lý do bảo mật hoặc đảm bảo hiệu suất của containers khi ghi một lượng lớn dữ liệu một cách không liên tục.

### 5. Dockerfile <a name="5"></a>

#### 5.1 Khái niệm

`Dockerfile` là một tập tin dạng text chứa một chuỗi các câu lệnh, chỉ thị để tạo nên một image. Dockerfile bao gồm các câu lệnh liên tiếp thực hiện tự động dựa trên một image có sẵn để tạo ra một image mới. 

#### 5.2 Cú pháp trong Dockerfile

```
# Comment
INSTRUCTION arguments
```
Trong đó:
- `INSTRUCTION`: Là các câu lệnh, chỉ thị được Docker quy định và toàn bộ những chỉ thị này phải được viết bằng chữ IN HOA.
- `arguments`: Phần nội dung của chỉ thị

Ví dụ:

```
RUN echo "Hello world!"
```

#### 5.3 Các câu lệnh/chỉ thị trong Dockerfile

`Dockerfile` chứa một tập hợp các câu lệnh bao gồm cả của Docker và các câu lệnh của OS. Trước hết, cần tìm hiểu rõ các câu lệnh của Dockerfile.

- **FROM**: Dựa trên một image có sẵn để tạo ra một image mới. Chỉ thị này phải được đặt ở đầu Dockerfile.
- **MAINTAINER**: (Tùy chọn) Điền thông tin của tác giả, người tạo ra image.
- **RUN**: Chỉ thị dùng để thực thi câu lệnh ở bên trong image
- **ADD**: Dùng để sao chép một file hoặc folder từ Host vào trong image. Có thể sử dụng một URL, Docker sẽ tải về thư mục đích bên trong image.
- **ENV**: Khởi tạo một biến môi trường bên trong image.
- **CMD**: Sử dụng để thực thi một câu lệnh khi tạo container được tạo từ image.
- **ENTRYPOINT**: Chỉ ra một câu lệnh được thực thi khi container chạy.
- **WORKDIR**:  Chỉ ra thư mục làm việc khi tạo image hoặc khi khởi chạy container
- **USER**: Xác định user (UID) thực thi các câu lệnh ở các chỉ thị CMD, RUN, ENTRYPOINT,... được xác định ở phía sau nó.
- **VOLUME**: Cho phép truy cập/liên kết thư mục giữa container với host.
- **EXPOSE**: Khai báo các Port Container sử dụng.
- **ARG**: Khai báo sử dụng tham số khi build image sử dụng câu lệnh `docker build` với cờ`--build-arg <varname>=<value>`

#### Chia sẻ một số Dockerfile  <a name="5.1"></a>

- KMS Server - https://gist.github.com/hoangdh/ed45d2c350a28ed3814e4f08d5fffe71
- NGINX RTMP - https://raw.githubusercontent.com/hoangdh/ghichep-StreamingVideo/master/Dockerfile

### 6. Docker-compose <a name="6"></a>
Ref: https://docs.docker.com/compose/overview/

#### 6.1 Khái niệm

`docker-compose` là một công cụ để tạo, xác định và chạy nhiều container có mối liên quan với nhau trong cùng một thời điểm; được khai báo trong một file với định dạng YAML. Khởi động tất cả các dịch vụ chỉ với 1 câu lệnh duy nhất.

Với 3 bước cơ bản như sau:

- Định nghĩa các ứng dụng thông qua `Dockerfile`
- Định nghĩa các ứng dụng chạy tách biệt và khởi động cùng nhau trong `docker-compose.yml`
- Thực thi câu lệnh `docker-compose up -d` để hoàn tất

Một file `docker-compose.xml` mẫu:

```
version: '3'
services:
  web:
    build: .
    ports:
    - "5000:5000"
    volumes:
    - .:/code
    - logvolume01:/var/log
    links:
    - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```

Sử dụng `docker-compose` để quản lý vòng đời ứng dụng cụ thể xem và quản lý trạng thái của các service (Start, stop, rebuild,...); chuyển log của các ứng dụng đang chạy.

#### 6.2 Tính năng, lợi ích

- Tạo ra nhiều môi trường riêng biệt trên cùng 1 host
- Dữ liệu được bảo toàn
- Chỉ khởi tạo lại khi có sư thay đổi
- Các thành phần và biến được sử dụng giữa các môi trường

**To be continued...**
