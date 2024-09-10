# UERANSim

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
