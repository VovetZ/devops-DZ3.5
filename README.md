# devops-DZ3.5
1. Узнайте о sparse (разряженных) файлах.
### Ответ
Разрежённый файл (англ. sparse file) — файл, в котором последовательности нулевых байтов заменены на информацию об этих последовательностях (список дыр).

Дыра (англ. hole) — последовательность нулевых байт внутри файла, не записанная на диск. Информация о дырах (смещение от начала файла в байтах и количество байт) хранится в метаданных ФС.
Преимущества:

- экономия дискового пространства. Использование разрежённых файлов считается одним из способов сжатия данных на уровне файловой системы;
- отсутствие временных затрат на запись нулевых байт;
- увеличение срока службы запоминающих устройств.
Недостатки:

- накладные расходы на работу со списком дыр;
- фрагментация файла при частой записи данных в дыры;
- невозможность записи данных в дыры при отсутствии свободного места на диске;
- невозможность использования других индикаторов дыр, кроме нулевых байт.

Следующие ФС поддерживают разрежённые файлы: BTRFS, NILFS, ZFS, NTFS[2], ext2, ext3, ext4, XFS, JFS, ReiserFS, Reiser4, UFS, Rock Ridge, UDF, ReFS, APFS, F2FS.

VirtualBox  поддерживает работу с разрежёнными файлами, это помогает экономить ресурсы хоста - для виртуалки выделяем N Гбайт дискового пространства, а по факту тратится ровно столько, сколько занято


2. Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?
### Ответ
Нет, не могут. В Linux каждый файл имеет уникальный идентификатор - индексный дескриптор (inode). Это число, которое однозначно идентифицирует файл в файловой системе. Жесткая ссылка и файл, для которой она создавалась имеют одинаковые inode. Поэтому жесткая ссылка имеет те же права доступа, владельца и время последней модификации, что и целевой файл.

3. Сделайте vagrant destroy на имеющийся инстанс Ubuntu. Замените содержимое Vagrantfile следующим:
```bash
Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-20.04"
  config.vm.provider :virtualbox do |vb|
    lvm_experiments_disk0_path = "/tmp/lvm_experiments_disk0.vmdk"
    lvm_experiments_disk1_path = "/tmp/lvm_experiments_disk1.vmdk"
    vb.customize ['createmedium', '--filename', lvm_experiments_disk0_path, '--size', 2560]
    vb.customize ['createmedium', '--filename', lvm_experiments_disk1_path, '--size', 2560]
    vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk0_path]
    vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 2, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk1_path]
  end
end
```
Данная конфигурация создаст новую виртуальную машину с двумя дополнительными неразмеченными дисками по 2.5 Гб.
### Ответ
VM создана 

4. Используя fdisk, разбейте первый диск на 2 раздела: 2 Гб, оставшееся пространство.
### Ответ
```bash
root@vagrant:/home/vagrant# fdisk -l | more
..................
Disk /dev/sdb: 2.51 GiB, 2684354560 bytes, 5242880 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
........................

root@vagrant:/home/vagrant# fdisk /dev/sdb
........
root@vagrant:/home/vagrant# fdisk -l /dev/sdb
Disk /dev/sdb: 2.51 GiB, 2684354560 bytes, 5242880 sectors
Disk model: VBOX HARDDISK
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x6b863087

Device     Boot   Start     End Sectors  Size Id Type
/dev/sdb1          2048 4196351 4194304    2G 83 Linux
/dev/sdb2       4196352 5242879 1046528  511M 83 Linux
```

5. Используя sfdisk, перенесите данную таблицу разделов на второй диск.
### Ответ
```bash
```

6. Соберите mdadm RAID1 на паре разделов 2 Гб.
### Ответ
```bash
```

7. Соберите mdadm RAID0 на второй паре маленьких разделов.
### Ответ
```bash
```

8. Создайте 2 независимых PV на получившихся md-устройствах.
### Ответ
```bash
```

9. Создайте общую volume-group на этих двух PV.
### Ответ
```bash
```

10. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.
### Ответ
```bash
```

11. Создайте mkfs.ext4 ФС на получившемся LV.
### Ответ
```bash
```

12. Смонтируйте этот раздел в любую директорию, например, /tmp/new.
### Ответ
```bash
```

13. Поместите туда тестовый файл, например wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz.
### Ответ
```bash
```

14. Прикрепите вывод lsblk.
### Ответ
```bash
```

15. Протестируйте целостность файла:
```bash
root@vagrant:~# gzip -t /tmp/new/test.gz
root@vagrant:~# echo $?
0
```
### Ответ
```bash
```

16. Используя pvmove, переместите содержимое PV с RAID0 на RAID1.
### Ответ
```bash
```

17. Сделайте --fail на устройство в вашем RAID1 md.
### Ответ
```bash
```

18. Подтвердите выводом dmesg, что RAID1 работает в деградированном состоянии.
### Ответ
```bash
```

19. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:
```bash
root@vagrant:~# gzip -t /tmp/new/test.gz
root@vagrant:~# echo $?
0
```
### Ответ
```bash
```

20. Погасите тестовый хост, vagrant destroy.
### Ответ
```bash
```
