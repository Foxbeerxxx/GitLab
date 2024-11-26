# Домашнее задание к занятию "`Название занятия`" - `Фамилия и имя студента`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1

`Разверните GitLab локально, используя Vagrantfile и инструкцию, описанные в` `этом репозитории.`
`Создайте новый проект и пустой репозиторий в нём.`
`Зарегистрируйте gitlab-runner для этого проекта и запустите его в режиме` `Docker. Раннер можно регистрировать и запускать на той же виртуальной машине,` `на которой запущен GitLab.`
`В качестве ответа в репозиторий шаблона с решением добавьте скриншоты с настройками раннера в проекте.`

1. `Добавляем в Хост файл доменное имя Локального GitLab`
```
echo '192.168.56.10    gitlab.localdomain gitlab' >> /etc/hosts
```
2. `Копирую репозиторий себе для удобной работы на пк`
`Копирую репозиторий себе для удобной работы на пк перед этим сделав себе fork`
`https://github.com/Foxbeerxxx/sdvps-materials-test/tree/main/gitlab`
3. `Меняю Vagrantfile`
```
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"

  # Устанавливаем частную сеть с IP
  config.vm.network "private_network", ip: "192.168.56.10"

  # Конфигурация для поставщика VirtualBox
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2000"
  end

  # Устанавливаем скрипт выполняемый при провижнинге
  config.vm.provision "shell", inline: <<-SHELL
    export DEBIAN_FRONTEND=noninteractive
    apt-get update
    # Установка Docker и Docker Compose
    apt-get install -y docker.io docker-compose
    # Установка GitLab
    apt-get install -y curl openssh-server ca-certificates tzdata perl
    curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash
    sudo EXTERNAL_URL="http://gitlab.localdomain" apt-get install gitlab-ee
    # Предварительная загрузка Docker-образов
    docker pull gitlab/gitlab-runner:latest
    docker pull sonarsource/sonar-scanner-cli:latest
    docker pull golang:1.17
    docker pull docker:latest
    # Настройка sysctl для SonarQube
    sysctl vm.max_map_count=262144
    # Добавление записей в /etc/hosts
    echo -e "192.168.56.10\tubuntu-bionic\tubuntu-bionic" >> /etc/hosts
    echo -e "192.168.56.10\tgitlab.localdomain\tgitlab" >> /etc/hosts
  SHELL
end
```
4. `Пробую запускать`
```
VAGRANT_EXPERIMENTAL="disks" vagrant up --provider=virtualbox
```
5. `Виртуалка создается и начинает работать, только из за 2гб оперативки сервис грузится долго`
![1](https://github.com/Foxbeerxxx/GitLab/blob/main/img/img1.png)`

![2](https://github.com/Foxbeerxxx/GitLab/blob/main/img/img2.png)`

6. `Как итог получилось добавить памяти до почти 6гб и GitLab стартанул`

![3](https://github.com/Foxbeerxxx/GitLab/blob/main/img/img3.png)`

6. `Заходим и логинимся`

![4](https://github.com/Foxbeerxxx/GitLab/blob/main/img/img4.png)`

7. `Создаю свой пустой проект MY_PROJECT`

![5](https://github.com/Foxbeerxxx/GitLab/blob/main/img/img5.png)`

8. `Зарегистрировал gitlab-runner для этого проекта и запустил его в режиме Docker.`

![6](https://github.com/Foxbeerxxx/GitLab/blob/main/img/img6.png)`


8. `Появился в  gitlab-runner .`

![7](https://github.com/Foxbeerxxx/GitLab/blob/main/img/img7.png)`




### Задание 2

`Что нужно сделать:`

`Запушьте репозиторий на GitLab, изменив origin. Это изучалось на занятии поGit.`
`Создайте .gitlab-ci.yml, описав в нём все необходимые, на ваш взгляд, этапы.`



1. `Создаем файл и наполняем его .gitlab-ci.yml`
```
stages:
  - test
  - build

test:
  stage: test
  image: golang:1.17
  script: 
   - go test .

build:
  stage: build
  image: docker:latest
  script:
   - docker build .

```

2. `Затем Push его в наш локальный Gitlab`

3. `Сработал тригер и началась сборка`
`Как и в видео лекции все по домашщнему заданию`
![8](https://github.com/Foxbeerxxx/GitLab/blob/main/img/img8.png)`



