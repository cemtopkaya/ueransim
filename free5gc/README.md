# UE ve RAN Simülatörü

UERanSim'in çalıştırılabilir dosyaları:
1. **`nr-gnb`** : 5G gNB (RAN) için ana yürütülebilir dosya
1. **`nr-ue`** : 5G UE için ana yürütülebilir dosya
1. **`nr-cli`** : 5G gNB ve UE için CLI aracı
1. **`nr-binder`** : UE'nin internet bağlantısını değerlendirmeye yönelik bir araç.
1. UE ve gNB'yi kullanmaya başlamak için çalıştırın `nr-gnb` ve `nr-ue`.
1. `nr-binder` ve `libdevbnd.so` yalnızca UE'lerin internet bağlantısının keyfi bir uygulamaya bağlanması için gereklidir ve genellikle kullanılmaz.

### TUN ve TAP
**TUN (network *TUN*nel)** , bir sanal ağ adaptörüdür. Bu, sanal bir ağ kartı gibidir ancak gerçek bir fiziki donanım yerine tamamen yazılım temellidir. TUN cihazları, IP tabanlı ağ trafiğini kullanıcı modunda (user plane) çalıştırılan bir programa iletir ve tersi şekilde programdan gelen IP paketlerini çekirdek üzerinden (kernel plane) yönlendirir. Bu, genellikle VPN (Virtual Private Network) gibi uygulamalarda kullanılır. **TUN** cihazları **IP trafiğini işlerken**, **TAP** cihazları **Ethernet çerçevelerini işler**, yani daha düşük seviyede veri iletimini simüle eder.

### /dev/net/tun
`/dev/net/tun`, Linux çekirdeğinde sanal bir ağ arayüzü oluşturmak için kullanılan bir sanal cihaz sürücüsüdür. Bu cihaz, uygulamaların IP paketlerini doğrudan işletim sisteminin ağ yığınına gönderebilmesini sağlar. Bu tür cihazlar genellikle VPN, tünelleme ve sanal ağ oluşturma gibi işlemlerde kullanılır. Konteyner olarak ayaklandırmak için ona bazı yetenekler vermeniz ve oluşturduğunuz konteynerelere bazı aygıtlar (`/dev/net/tun`) tanımlamanız gerekir, aksi takdirde kullanamazsınız. 


UERANSIM, 5G RAN (Radio Access Network) simülatörü konteyner olarak çalıştırıldığında, `/dev/net/tun` cihazının konteyner içinde mevcut olması gerekir. Bunun nedeni, UERANSIM'in sanal UE (User Equipment) ve gNB (gNodeB) arasındaki iletişimi simüle etmek için bu cihazı kullanmasıdır.

- Eğer Docker Compose kullanıyorsanız, şunu eklemeniz gerekir:
  ```yaml
  cap_add:
    - NET_ADMIN
  devices:
    - "/dev/net/tun"
  ```

- Eğer `docker run` ile başlatıyorsanız `--cap-add=NET_ADMIN --device /dev/net/tun` bayraklarını eklemelisiniz.


## UERanSim Derlemek
### 1- Xenial'da cmake klasik olarak 3.5 kuruyor, bu yüzden cmake version 3.17 kurulmalı:
```shell
wget https://github.com/Kitware/CMake/releases/download/v3.17.0/cmake-3.17.0.tar.gz
tar -zxvf cmake-3.17.0.tar.gz
cd cmake-3.17.0
./bootstrap
make
sudo make install
cmake --version # 3.17 olduğu görülür
```

### 2- gcc-9 g++-9 kurulumu:
Gerekli compilerlar aşağıdaki şekilde kurulur
```shell
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get install gcc-9 g++-9
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-9 60
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-9 60
sudo update-alternatives --config gcc
sudo update-alternatives --config g++
gcc --version #verify that gcc-9
g++ --version #verify that g++-9
```

### 3- UERANSIM build alınması 
Yukarıdaki adımlar(0-1-2) izlendikten sonra standart bir cmake buildi alınır
```shell
git clone https://github.com/aligungr/UERANSIM.git
cd UERANSIM
mkdir build
cd build
cmake ..
make #verify that nr-cli nr-gnb nr-ue constructed in the build directory
```

Projeyi başarıyla derledikten sonra, çıktı ikili dosyaları klasöre kopyalanacaktır `~/UERANSIM/build`. 
Ve aşağıdaki dosyaları görmelisiniz:

1. **`nr-gnb`** : 5G gNB (RAN) için ana yürütülebilir dosya
1. **`nr-ue`** : 5G UE için ana yürütülebilir dosya
1. **`nr-cli`** : 5G gNB ve UE için CLI aracı
1. **`nr-binder`** : UE'nin internet bağlantısını değerlendirmeye yönelik bir araç.
1. **`libdevbnd.so`** : `nr-binder` için dinamik bir kütüphane

UE ve gNB'yi kullanmaya başlamak için çalıştırın `nr-gnb` ve `nr-ue`.
`nr-binder` ve `libdevbnd.so` yalnızca UE'lerin internet bağlantısının keyfi bir uygulamaya bağlanması için gereklidir ve genellikle kullanılmaz.
