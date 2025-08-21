# 🚀 Ceph Cluster Deployment with Ansible

<p align="center">
  <img src="https://img.shields.io/badge/Ansible-Automation-red?logo=ansible&logoColor=white" alt="Ansible" />
  <img src="https://img.shields.io/badge/Ceph-Storage-blue?logo=ceph&logoColor=white" alt="Ceph" />
  <img src="https://img.shields.io/badge/Podman-Container-lightgrey?logo=podman&logoColor=white" alt="Podman" />
  <img src="https://img.shields.io/badge/HAProxy-Load%20Balancer-green?logo=haproxy&logoColor=white" alt="HAProxy" />
  <img src="https://img.shields.io/badge/AWS%20CLI-Integration-orange?logo=amazonaws&logoColor=white" alt="AWS CLI" />
</p>

📦 **ceph-ansible** adalah playbook Ansible untuk **deploy cluster Ceph 3 node (ceph-1, ceph-2, ceph-3)** lengkap dengan **Rados Gateway (S3-compatible)** dan **HAProxy** sebagai load balancer.

---

## 📂 Struktur Direktori

```bash
ceph-ansible/
├── group_vars
│   └── all.yml                # Variabel global cluster
├── inventory
│   └── hosts.ini              # Daftar host (ceph-1, ceph-2, ceph-3)
├── roles
│   ├── common                 # Install paket dasar (chrony, lvm2, dll)
│   ├── cephadm_install        # Install cephadm & pull image Ceph
│   ├── ceph_bootstrap         # Bootstrap cluster dari ceph-1
│   ├── ceph_add_hosts         # Tambah node ceph-2 & ceph-3
│   ├── ceph_services          # Deploy mon, mgr, osd
│   ├── ceph_osd               # Setup OSD di setiap node
│   ├── ceph_rgw               # Setup RGW (Rados Gateway)
│   └── haproxy                # Setup HAProxy (endpoint tunggal)
└── site.yml                   # Entry point playbook
```

---

## ⚙️ Prasyarat

* 🐧 Ubuntu 22.04 LTS (disarankan)
* 🐙 Ansible sudah terinstall di **node ansible**
* 🔑 Akses SSH ke semua node (root/password)
* 📡 Internet untuk pull image Ceph & install paket

---

## 🚀 Cara Pakai

### 1️⃣ Clone Repo

```bash
git clone https://github.com/username/ceph-ansible.git
cd ceph-ansible
```

### 2️⃣ Konfigurasi Inventory

Edit file `inventory/hosts.ini`:

```ini
[ceph_nodes]
ceph-1 ansible_host=103.168.146.210 ansible_user=root ansible_password=yourpass
ceph-2 ansible_host=103.168.146.206 ansible_user=root ansible_password=yourpass
ceph-3 ansible_host=103.168.146.207 ansible_user=root ansible_password=yourpass
```

### 3️⃣ Jalankan Playbook

```bash
ansible-playbook -i inventory/hosts.ini site.yml
```

---

## 🔑 Akses Dashboard

Setelah bootstrap berhasil, dashboard bisa diakses di:

```
https://103.168.146.210:8443/
```

Login default:

* **User**: `root`
* **Password**: `Andikaferdi11`

---

## 🌐 HAProxy Endpoint

Setelah role HAProxy jalan, endpoint S3 bisa diakses via:

```
http://103.168.146.210:7480
```

---

## 🪣 Contoh Setup User S3

```bash
radosgw-admin user create --uid="andika" --display-name="Andika"
```

Catat **access\_key** dan **secret\_key** untuk AWS CLI.

---

## 🐳 Test AWS CLI

```bash
aws configure
# Access Key ID: dari radosgw-admin
# Secret Access Key: dari radosgw-admin
# Region: us-east-1
# Output: json

aws --endpoint-url http://103.168.146.210:7480 s3 mb s3://test-bucket
aws --endpoint-url http://103.168.146.210:7480 s3 cp /etc/hosts s3://test-bucket/
aws --endpoint-url http://103.168.146.210:7480 s3 ls s3://test-bucket
```

---

## 🎯 Tujuan Project

✅ Automasi full deployment Ceph cluster dengan Ansible.
✅ S3-compatible object storage via RGW.
✅ Load balancing dengan HAProxy.
✅ Dokumentasi lengkap agar mudah dipahami pemula.

---

## 📜 Lisensi

Proyek ini dirilis di bawah [MIT License](./LICENSE).
