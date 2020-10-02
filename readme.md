# Multipass + MicroK8s

[![N|Solid](https://camo.githubusercontent.com/113146b62d7ff10083a06cd4d3630b9bfd1f0eb1/68747470733a2f2f696d672e736869656c64732e696f2f747769747465722f75726c3f7374796c653d736f6369616c2675726c3d6874747073253341253246253246726168756c646b6a61696e2e6769746875622e696f2532466769746875622d70726f66696c652d726561646d652d67656e657261746f72)](https://twitter.com/alysonpiresreal)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Configurando um ambiente Kubernetes com `MicroK8s` usando maquinas virtuais no `Multipass`

### Instalação e configuração do [Multipass](https://multipass.run/docs/installing-on-linux/) no Ubuntu 

```sh
$ sudo snap install multipass 
$ multipass launch -n k8s -m 2g -c 1 -d 10g
$ multipass launch -n node1 -m 1g -c 1 -d 5g
```

### Instalação e configuração do [MicroK8s](https://microk8s.io//)
```sh
$ multipass exec k8s -- sudo snap install microk8s --classic --channel=1.18/stable
$ multipass exec k8s -- sudo usermod -aG microk8s ubuntu
$ multipass exec k8s -- sudo chown -fR ubuntu ~/.kube
$ multipass restart k8s
$ multipass exec k8s -- /snap/bin/microk8s.kubectl create deployment nginx --image nginx
$ multipass exec k8s -- /snap/bin/microk8s.kubectl get all --all-namespaces
$ multipass exec k8s -- echo "alias kubectl=microk8s.kubectl" >> ~/.bashrc
$ multipass exec k8s -- echo "source <(microk8s.kubectl completion bash)" >> ~/.bashrc
$ multipass exec k8s -- /snap/bin/microk8s.kubectl config view --raw

```
**Copie o resultado do comando acima e cole no arquivo ~/.kube/config da maquina local**

**OBS: Não esqueça de apontar o IP da maquina multipass no arquivo ~/.kube/config**

###### Para adicionar um node ao k8s master, basta executar o seguindo comando no master
```sh
$ multipass exec k8s -- /snap/bin/microk8s add-node
```
 
- Copie a linha "microk8s join 10.22.205.56:25000/ZiJPJYGtUwzUQuVMXAXhKeOfLCcGTXCT"
- Execute o seguinte comando no node

```sh
$ multipass exec node1 -- /snap/bin/microk8s join 10.22.205.56:25000/ZiJPJYGtUwzUQuVMXAXhKeOfLCcGTXCT 
```

###### Verifique se o node1 foi adicionado ao cluster
```sh
$ kubectl get nodes
```
###### Removendo um node do cluster
```sh
$ multipass exec k8s -- /snap/bin/microk8s remove-node node1 
$ multipass exec node1 -- /snap/bin/microk8s leave
```
###### Habilitando resolução de nomes no MicroK8s
```sh
$ multipass exec k8s -- /snap/bin/microk8s enable dns
```

Limitação do `Multipass`:
- O Multipass ainda não dá suporte para alterar recursos da maquina virtual, como cpu e memória ram

###### Existe uma alternativa para alterar as configurações da maquina virtual no Multipass
```sh
$ sudo systemctl stop snap.multipass.multipassd.service
$ sudo vi /var/snap/multipass/common/data/multipassd/multipassd-vm-instances.json 
$ sudo systemctl start snap.multipass.multipassd.service
```
License
----
MIT

**Free Software, Hell Yeah!**
