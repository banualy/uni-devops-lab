#University Student Portal

## haqqında

Layihənin məqsədi universitet tələbə portalını Git, Docker və Ansible vasitəsilə avtomatlaşdırılmış şəkildə serverə yerləşdirməkdir.

## texnologiyalar

Git, Docker, Docker Compose (Flask və Redis servislərini işə salmaq üçün), Ansible, Python Flask

## struktur

```text
uni-devops-lab/
├── app/
│   ├── app.py
│   ├── requirements.txt
│   └── Dockerfile
├── ansible/
│   ├── inventory.ini
│   ├── deploy.yml
│   ├── group_vars/
│   └── templates/
├── compose.yml
├── .gitignore
└── README.md
```

## Docker

Tətbiq `python:3.12-slim` əsasında Docker image şəklində hazırlanıb.

Image adı: `student-portal:1.0`

Flask tətbiqi 8080 portunda, Redis isə ayrıca konteynerdə işləyir. Redis məlumatlarının itməməsi üçün named volume istifadə olunub.

Tətbiqi işə salmaq üçün:

```bash
sudo docker compose up -d --build
```

## Ansible

Ansible vasitəsilə App Server-də Docker quraşdırılır və aktivləşdirilir. Tətbiq faylları serverə köçürülür, Docker Compose servisləri başladılır və `/health` endpoint-i yoxlanılır.

Playbook-u işə salmaq üçün:

```bash
ansible-playbook -i ansible/inventory.ini ansible/deploy.yml -K
```

Tətbiqə daxil olmaq üçün:

- Əsas səhifə: `http://172.16.107.131:8080`
- Health: `http://172.16.107.131:8080/health`
- Visits: `http://172.16.107.131:8080/visits`

## Qarşılaşılan problemlər

App Server Red Hat-a qeydiyyatdan keçirilmədiyi üçün paketləri yükləyə bilmirdim. `subscription-manager register` əmri ilə serveri qeydiyyatdan keçirdikdən sonra bu problem həll olundu.

Daha sonra Docker Hub-dan image-ləri yükləyərkən DNS xətası yarandı.  Ansible vasitəsilə DNS ünvanlarını `8.8.8.8` və `1.1.1.1` olaraq dəyişdim,  image-lərin yüklənməsi mümkün oldu.

İlk başdan `main` branch-i ayrıca yaratmamışdım.  `main` branch Stage 1-in commit-i ilə eyni idi və Pull Request yaratmaq olmurdu. Boş bir ilkin commit yaradaraq `main` branch-i ayrıca düzəltdim. Sonra `rebase` elədim, dəyişikliklər yeni `main` branch-dən davam etsin. Mergeləri elədikdən sonra sonda  local `main` branch-i `git pull --ff-only origin main`  ilə githubdakı `main` branch-ə fast-forward elədim.
