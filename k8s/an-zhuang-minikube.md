# 安装minikube

# 简介

我们刚学习k8s之际 不能光靠文档不操作, 所以需要在本机搭建一个mini的k8s环境来进行学习

minikube的说明文档: https://minikube.sigs.k8s.io/docs/

# 安装过程

### 检查Linux是否支持虚拟化技术

```bash
# 命令返回为空 则此linux支持虚拟化技术
$ grep -E --color 'vmx|svm' /proc/cpuinfo
```

### 安装kubectl

tips: 之力官网文档都是使用google源下载依赖, 所以这里需要切换成阿里源

```bash
sudo apt-get update && sudo apt-get install -y apt-transport-https
curl -s https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
```

执行第二个`sudo apt-get update`时出现了warm 这里先忽略

```bash
root@serve-404mzk:~# apt update
Hit:1 http://mirrors.cloud.aliyuncs.com/ubuntu bionic InRelease
Hit:2 http://mirrors.cloud.aliyuncs.com/ubuntu bionic-updates InRelease
Hit:3 http://mirrors.cloud.aliyuncs.com/ubuntu bionic-security InRelease
Hit:4 https://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial InRelease
Hit:5 https://download.docker.com/linux/ubuntu bionic InRelease
Reading package lists... Done
Building dependency tree
Reading state information... Done
108 packages can be upgraded. Run 'apt list --upgradable' to see them.
W: Target Packages (main/binary-amd64/Packages) is configured multiple times in /etc/apt/sources.list:62 and /etc/apt/sources.list.d/kubernetes.list:1
W: Target Packages (main/binary-i386/Packages) is configured multiple times in /etc/apt/sources.list:62 and /etc/apt/sources.list.d/kubernetes.list:1
W: Target Packages (main/binary-all/Packages) is configured multiple times in /etc/apt/sources.list:62 and /etc/apt/sources.list.d/kubernetes.list:1
W: Target Translations (main/i18n/Translation-en) is configured multiple times in /etc/apt/sources.list:62 and /etc/apt/sources.list.d/kubernetes.list:1
W: Target CNF (main/cnf/Commands-amd64) is configured multiple times in /etc/apt/sources.list:62 and /etc/apt/sources.list.d/kubernetes.list:1
W: Target CNF (main/cnf/Commands-all) is configured multiple times in /etc/apt/sources.list:62 and /etc/apt/sources.list.d/kubernetes.list:1
W: Target Packages (main/binary-amd64/Packages) is configured multiple times in /etc/apt/sources.list:62 and /etc/apt/sources.list.d/kubernetes.list:1
W: Target Packages (main/binary-i386/Packages) is configured multiple times in /etc/apt/sources.list:62 and /etc/apt/sources.list.d/kubernetes.list:1
W: Target Packages (main/binary-all/Packages) is configured multiple times in /etc/apt/sources.list:62 and /etc/apt/sources.list.d/kubernetes.list:1
W: Target Translations (main/i18n/Translation-en) is configured multiple times in /etc/apt/sources.list:62 and /etc/apt/sources.list.d/kubernetes.list:1
W: Target CNF (main/cnf/Commands-amd64) is configured multiple times in /etc/apt/sources.list:62 and /etc/apt/sources.list.d/kubernetes.list:1
W: Target CNF (main/cnf/Commands-all) is configured multiple times in /etc/apt/sources.list:62 and /etc/apt/sources.list.d/kubernetes.list:1
```

执行`kubectl version`查看是否安装成功

正常来说看到类似如下的命令 就证明kubectl安装成功了

```bash
kubectl version
Client Version: version.Info{Major:"1", Minor:"18", GitVersion:"v1.18.2", GitCommit:"52c56ce7a8272c798dbc29846288d7cd9fbae032", GitTreeState:"clean", BuildDate:"2020-04-16T11:56:40Z", GoVersion:"go1.13.9", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"18", GitVersion:"v1.18.0", GitCommit:"9e991415386e4cf155a24b1da15becaa390438d8", GitTreeState:"clean", BuildDate:"2020-03-25T14:50:46Z", GoVersion:"go1.13.8", Compiler:"gc", Platform:"linux/amd64"}
```

笔者这里还犯了一个错误, 找一个work用户去安装`kubectl`, 然后用`root`执行`kubectl version`

就报了以下错误

```bash
$ kubectl version
error: Missing or incomplete configuration info.  Please point to an existing, complete config file:

  1. Via the command-line flag --kubeconfig
  2. Via the KUBECONFIG environment variable
  3. In your home directory as ~/.kube/config

To view or setup config directly use the 'config' command.
```

咦 好像说我们配置文件不存在

其实是安装`kubectl`的时候 默认会给安装的用户的这个目录去加配置文件`$HOME/.kube`

所以我用root执行`kubectrl version`会报配置文件不存在

如果需要让普通用户可以运行 kubectl，请运行如下命令，其实这也是 kubeadm init 输出的一部分：

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

或者，如果您是 root 用户，则可以运行：

```bash
export KUBECONFIG=/etc/kubernetes/admin.conf
```

这里的参考链接为

- https://kubernetes.io/zh/docs/setup/independent/create-cluster-kubeadm/
- https://kubernetes.io/zh/docs/tasks/tools/install-minikube/

### 安装minikube

```bash
$ curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
  && chmod +x minikube
$ sudo mkdir -p /usr/local/bin/
$ sudo install minikube /usr/local/bin/
```

minikube需要关闭swap功能, 并且重启电脑

```bash
$ vim /etc/fstab
UUID=6392db53-9906-438b-8413-xxx /               ext4    errors=remount-ro 0       1
#/swapfile                                 none            swap    sw              0       0
/dev/fd0        /media/floppy0  auto    rw,user,noauto,exec,utf8 0       0
```

设置阿里云docker源

如果我们直接`minikube start --vm-driver=none`会受到一个警告

❗  This bare metal machine is having trouble accessing https://k8s.gcr.io
ð¡  To pull new external images, you may need to configure a proxy: https://minikube.sigs.k8s.io/docs/reference/networking/proxy/

所以需要设置minikube的镜像源

```bash
minikube start \
  --vm-driver=none \
  --image-mirror-country=cn \
  --registry-mirror='https://t9ab0rkd.mirror.aliyuncs.com' \
  --image-repository='registry.cn-hangzhou.aliyuncs.com/google_containers' 
#kubeadm init --image-repository='registry.cn-hangzhou.aliyuncs.com/google_containers'

ð  minikube v1.9.2 on Ubuntu 18.04
✨  Using the none driver based on existing profile
✅  Using image repository registry.cn-hangzhou.aliyuncs.com/google_containers
ð  Starting control plane node  in cluster minikube
ð  Updating the running none "minikube" bare metal machine ...
ℹ️  OS release is Ubuntu 18.04.3 LTS
ð³  Preparing Kubernetes v1.18.0 on Docker 19.03.6 ...
    ▪ kubelet.resolv-conf=/run/systemd/resolve/resolv.conf
ð  Enabling addons: default-storageclass, storage-provisioner
ð¤¹  Configuring local host environment ...

❗  The 'none' driver is designed for experts who need to integrate with an existing VM
ð¡  Most users should use the newer 'docker' driver instead, which does not require root!
ð  For more information, see: https://minikube.sigs.k8s.io/docs/reference/drivers/none/

❗  kubectl and minikube configuration will be stored in /root
❗  To use kubectl or minikube commands as your own user, you may need to relocate them. For example, to overwrite your own settings, run:

    ▪ sudo mv /root/.kube /root/.minikube $HOME
    ▪ sudo chown -R $USER $HOME/.kube $HOME/.minikube

ð¡  This can also be done automatically by setting the env var CHANGE_MINIKUBE_NONE_USER=true
ð  Done! kubectl is now configured to use "minikube"
```

虽然有些警告,但是执行下`minikube status`, 如果为下文, 基本就是跑起来了

```bash
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

# 遇到的问题

### 

# 参考链接

- 安装kubectrl: https://github.com/islishude/blog/issues/212
- 安装minikube: https://kubernetes.io/zh/docs/tasks/tools/install-minikube/
- 待尝试: 中国化的安装minikube