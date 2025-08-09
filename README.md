# Домашнее задание к занятию 6 «Создание собственных модулей» - Мельник Юрий Александрович

## Подготовка к выполнению
1. Создайте пустой публичный репозиторий `my_own_collection`. - https://github.com/ysatii/my_own_collection
2. Скачайте репозиторий Ansible: `git clone https://github.com/ansible/ansible.git` по любому, удобному вам пути.
3. Зайдите в директорию Ansible: `cd ansible`.
4. Создайте виртуальное окружение: `python3 -m venv venv`.
5. Активируйте виртуальное окружение: `. venv/bin/activate`. Дальнейшие действия производятся только в виртуальном окружении.
6. Установите зависимости `pip install -r requirements.txt`.
7. Запустите настройку окружения `. hacking/env-setup`.
8. Если все шаги прошли успешно — выйдите из виртуального окружения `deactivate`.
9. Ваше окружение настроено. Чтобы запустить его, нужно находиться в директории `ansible` и выполнить конструкцию `. venv/bin/activate && . hacking/env-setup`.


## Выполнение подготовки к работе

1. Создайте пустой публичный репозиторий в своём любом проекте: `my_own_collection`.
2. Скачайте репозиторий Ansible: `git clone https://github.com/ansible/ansible.git` по любому, удобному вам пути.
3. Зайдите в директорию Ansible: `cd ansible`,
   Создайте виртуальное окружение: `python3 -m venv venv`.
   Активируйте виртуальное окружение: `. venv/bin/activate`. 
   

   Дальнейшие действия производятся только в виртуальном окружении.
   Установите зависимости `pip install -r requirements.txt`.
   Запустите настройку окружения `. hacking/env-setup`.
   Если все шаги прошли успешно — выйдите из виртуального окружения `deactivate`.
   ![рисунок 1](https://github.com/ysatii/ansible-hw6/blob/main/img/img1.jpg)
   ![рисунок 2](https://github.com/ysatii/ansible-hw6/blob/main/img/img2.jpg)

4. Окружение настроено. Чтобы запустить его, нужно находиться в директории `ansible` и выполнить конструкцию `. venv/bin/activate && . hacking/env-setup`.





## Основная часть

Ваша цель — написать собственный module, который вы можете использовать в своей role через playbook. Всё это должно быть собрано в виде collection и отправлено в ваш репозиторий.

**Шаг 1.** В виртуальном окружении создайте новый `my_own_module.py` файл.

**Шаг 2.** Наполните его содержимым:

```python
#!/usr/bin/python

# Copyright: (c) 2018, Terry Jones <terry.jones@example.org>
# GNU General Public License v3.0+ (see COPYING or https://www.gnu.org/licenses/gpl-3.0.txt)
from __future__ import (absolute_import, division, print_function)
__metaclass__ = type

DOCUMENTATION = r'''
---
module: my_test

short_description: This is my test module

# If this is part of a collection, you need to use semantic versioning,
# i.e. the version is of the form "2.5.0" and not "2.4".
version_added: "1.0.0"

description: This is my longer description explaining my test module.

options:
    name:
        description: This is the message to send to the test module.
        required: true
        type: str
    new:
        description:
            - Control to demo if the result of this module is changed or not.
            - Parameter description can be a list as well.
        required: false
        type: bool
# Specify this value according to your collection
# in format of namespace.collection.doc_fragment_name
extends_documentation_fragment:
    - my_namespace.my_collection.my_doc_fragment_name

author:
    - Your Name (@yourGitHubHandle)
'''

EXAMPLES = r'''
# Pass in a message
- name: Test with a message
  my_namespace.my_collection.my_test:
    name: hello world

# pass in a message and have changed true
- name: Test with a message and changed output
  my_namespace.my_collection.my_test:
    name: hello world
    new: true

# fail the module
- name: Test failure of the module
  my_namespace.my_collection.my_test:
    name: fail me
'''

RETURN = r'''
# These are examples of possible return values, and in general should use other names for return values.
original_message:
    description: The original name param that was passed in.
    type: str
    returned: always
    sample: 'hello world'
message:
    description: The output message that the test module generates.
    type: str
    returned: always
    sample: 'goodbye'
'''

from ansible.module_utils.basic import AnsibleModule


def run_module():
    # define available arguments/parameters a user can pass to the module
    module_args = dict(
        name=dict(type='str', required=True),
        new=dict(type='bool', required=False, default=False)
    )

    # seed the result dict in the object
    # we primarily care about changed and state
    # changed is if this module effectively modified the target
    # state will include any data that you want your module to pass back
    # for consumption, for example, in a subsequent task
    result = dict(
        changed=False,
        original_message='',
        message=''
    )

    # the AnsibleModule object will be our abstraction working with Ansible
    # this includes instantiation, a couple of common attr would be the
    # args/params passed to the execution, as well as if the module
    # supports check mode
    module = AnsibleModule(
        argument_spec=module_args,
        supports_check_mode=True
    )

    # if the user is working with this module in only check mode we do not
    # want to make any changes to the environment, just return the current
    # state with no modifications
    if module.check_mode:
        module.exit_json(**result)

    # manipulate or modify the state as needed (this is going to be the
    # part where your module will do what it needs to do)
    result['original_message'] = module.params['name']
    result['message'] = 'goodbye'

    # use whatever logic you need to determine whether or not this module
    # made any modifications to your target
    if module.params['new']:
        result['changed'] = True

    # during the execution of the module, if there is an exception or a
    # conditional state that effectively causes a failure, run
    # AnsibleModule.fail_json() to pass in the message and the result
    if module.params['name'] == 'fail me':
        module.fail_json(msg='You requested this to fail', **result)

    # in the event of a successful module execution, you will want to
    # simple AnsibleModule.exit_json(), passing the key/value results
    module.exit_json(**result)


def main():
    run_module()


if __name__ == '__main__':
    main()
```
Или возьмите это наполнение [из статьи](https://docs.ansible.com/ansible/latest/dev_guide/developing_modules_general.html#creating-a-module).

**Шаг 3.** Заполните файл в соответствии с требованиями Ansible так, чтобы он выполнял основную задачу: module должен создавать текстовый файл на удалённом хосте по пути, определённом в параметре `path`, с содержимым, определённым в параметре `content`.
```
#!/usr/bin/python

# Copyright: (c) 2025, lamer lamer <lamer@example.com>
# GNU General Public License v3.0+
from __future__ import (absolute_import, division, print_function)
__metaclass__ = type

import os
from ansible.module_utils.basic import AnsibleModule

DOCUMENTATION = r'''
---
module: my_own_module

short_description: Create a file with specified content

version_added: "1.0.0"

description: >
    This module creates a text file at a given path on the remote host with the specified content.
    If the file already exists with the same content, nothing is changed.

options:
    path:
        description:
            - Path to the file to be created.
        required: true
        type: str
    content:
        description:
            - Content to be written to the file.
        required: true
        type: str

author:
    - lamer lamer (@yourGitHubHandle)
'''

EXAMPLES = r'''
- name: Create a file with custom content
  my_own_namespace.yandex_cloud_elk.my_own_module:
    path: /tmp/hello.txt
    content: "Hello, Ansible!"
'''

RETURN = r'''
path:
    description: The path where the file was created or verified.
    type: str
    returned: always
    sample: "/tmp/hello.txt"
changed:
    description: Whether the file was changed.
    type: bool
    returned: always
    sample: true
message:
    description: Status message.
    type: str
    returned: always
    sample: "File created"
'''


def run_module():
    module_args = dict(
        path=dict(type='str', required=True),
        content=dict(type='str', required=True)
    )

    result = dict(
        changed=False,
        path='',
        message=''
    )

    module = AnsibleModule(
        argument_spec=module_args,
        supports_check_mode=True
    )

    path = module.params['path']
    content = module.params['content']
    result['path'] = path

    if module.check_mode:
        if not os.path.exists(path):
            result['changed'] = True
        else:
            with open(path, 'r') as f:
                existing = f.read()
            if existing != content:
                result['changed'] = True
        module.exit_json(**result)

    try:
        changed = True
        if os.path.exists(path):
            with open(path, 'r') as f:
                existing = f.read()
            if existing == content:
                changed = False

        if changed:
            with open(path, 'w') as f:
                f.write(content)

        result['changed'] = changed
        result['message'] = "File created or updated" if changed else "No changes made"
    except Exception as e:
        module.fail_json(msg=str(e), **result)

    module.exit_json(**result)


def main():
    run_module()


if __name__ == '__main__':
    main()

```



**Шаг 4.** Проверьте module на исполняемость локально.
получаем ошибку что нужен питон 3.12
'''
ansible localhost -m my_own_module -a "path=/tmp/test.txt content='Hello'" -M ~/my_ansible_module -c local
ERROR: Ansible requires Python 3.12 or newer on the controller. Current version: 3.8.10 (default, Mar 18 2025, 20:04:55) [GCC 9.4.0]
'''
исправим ошибку 

- Выйти из текущего окружения **deactivate**

- Перейти в папку с Ansible  **cd ~/Рабочий\ стол/ansible-hw6/module/ansible**

- Удалить старое окружение (оно на Python 3.8)  **rm -rf venv**

- Создать новое на Python 3.12 **python3.12 -m venv venv**

- Активировать окружение **. venv/bin/activate** 

- Установить зависимости 
  **pip install --upgrade pip**
  **pip install -r requirements.txt**

- Настроить Ansible **. hacking/env-setup** 

- Проверить версию Python и Ansible
  **python --version**
  **ansible --version**

сам файл необходимо переместить в папку  **/home/lamer/Рабочий стол/ansible-hw6/module/ansible/lib/ansible/modules/my_own_module.py**

выполним команду 

**ansible localhost -m my_own_module   -a "path=/tmp/test.txt content='Hello'"   -M ~/my_ansible_module -c local**
![рисунок 3](https://github.com/ysatii/ansible-hw6/blob/main/img/img3.jpg)

файл успешно содал по указанному пути с указанным содержимым
![рисунок 4](https://github.com/ysatii/ansible-hw6/blob/main/img/img4.jpg)


**Шаг 5.** Напишите single task playbook и используйте module в нём.
листинг test.yml
```
- name: Test custom module
  hosts: localhost
  connection: local
  gather_facts: false
  tasks:
    - name: Create a file using custom module
      my_own_module:
        path: /tmp/test.txt
        content: "Hello"
```
выполним команду 

```
ansible-playbook -M ~/my_ansible_module test.yml
```
файл  создается 
![рисунок 5](https://github.com/ysatii/ansible-hw6/blob/main/img/img5.jpg)



**Шаг 6.** Проверьте через playbook на идемпотентность.
повторно запустим 

```
ansible-playbook -M ~/my_ansible_module test.yml
```
файл  создается 
![рисунок 6](https://github.com/ysatii/ansible-hw6/blob/main/img/img6.jpg)
![рисунок 7](https://github.com/ysatii/ansible-hw6/blob/main/img/img7.jpg)


**Шаг 7.** Выйдите из виртуального окружения.
```
deactivate
```

**Шаг 8.** Инициализируйте новую collection: `ansible-galaxy collection init my_own_namespace.yandex_cloud_elk`.
готово 

**Шаг 9.** В эту collection перенесите свой module в соответствующую директорию.

   ~/my_ansible_module/my_own_module.py
 ```
 mkdir -p ~/my_own_namespace/yandex_cloud_elk/plugins/modules
 mv ~/my_ansible_module/my_own_module.py \
 ~/my_own_namespace/yandex_cloud_elk/plugins/modules/
```

**Шаг 10.** Single task playbook преобразуйте в single task role и перенесите в collection. У role должны быть default всех параметров module.
```
ansible-galaxy init ~/my_own_namespace/yandex_cloud_elk/roles/file_write
```
ответ - Role /home/lamer/my_own_namespace/yandex_cloud_elk/roles/file_write was created successfully

 defaults (параметры модуля по умолчанию)
```
cat > ~/my_own_namespace/yandex_cloud_elk/roles/file_write/defaults/main.yml <<'YAML'
path: "/tmp/hello_from_role.txt"
content: "Hello from role!"
YAML
```

 tasks (вызов модуля по FQCN)
```
cat > ~/my_own_namespace/yandex_cloud_elk/roles/file_write/tasks/main.yml <<'YAML'
- name: Write file via custom module
  my_own_namespace.yandex_cloud_elk.my_own_module:
    path: "{{ path }}"
    content: "{{ content }}"
YAML
```


**Шаг 11.** Создайте playbook для использования этой role.
cat > ~/my_own_namespace/yandex_cloud_elk/site.yml <<'YAML'
- name: Use role from collection
  hosts: localhost
  connection: local
  gather_facts: false
  roles:
    - my_own_namespace.yandex_cloud_elk.file_write
YAML


**Шаг 12.** Заполните всю документацию по collection, выложите в свой репозиторий, поставьте тег `1.0.0` на этот коммит.
```
# краткий README
cat > ~/my_own_namespace/yandex_cloud_elk/README.md <<'MD'
# yandex_cloud_elk collection

Contains:
- module: my_own_module (creates/updates a text file)
- role: file_write (wraps module with defaults)
- playbook: site.yml (demo)

## Role defaults
- path: target file path (default: /tmp/hello_from_role.txt)
- content: file content (default: "Hello from role!")

## Example
ansible-playbook site.yml
MD
```

 проставим версию 1.0.0 в galaxy.yml
sed -i 's/^version:.*$/version: "1.0.0"/' ~/my_own_namespace/yandex_cloud_elk/galaxy.yml

cd ~/my_own_namespace/yandex_cloud_elk
git add .
git commit -m "Initial collection 1.0.0 (module + role + docs)"
git tag 1.0.0
git remote add origin git@github.com:ysatii/my_own_collection.git
git push -u origin master
git push origin 1.0.0

https://github.com/ysatii/my_own_collection/tags

**Шаг 13.** Создайте .tar.gz этой collection: `ansible-galaxy collection build` в корневой директории collection.
cd ~/my_own_namespace/yandex_cloud_elk
ansible-galaxy collection build
 появится ./my_own_namespace-yandex_cloud_elk-1.0.0.tar.gz

 ответ 
 Created collection for my_own_namespace.yandex_cloud_elk at /home/lamer/my_own_namespace/yandex_cloud_elk/my_own_namespace-yandex_cloud_elk-1.0.0.tar.gz

![рисунок 8](https://github.com/ysatii/ansible-hw6/blob/main/img/img8.jpg)

**Шаг 14.** Создайте ещё одну директорию любого наименования, перенесите туда single task playbook и архив c collection.
```
mkdir -p ~/collection_test && cd ~/collection_test
cp ~/my_own_namespace/yandex_cloud_elk/my_own_namespace-yandex_cloud_elk-1.0.0.tar.gz .
cp ~/my_own_namespace/yandex_cloud_elk/site.yml .
```

**Шаг 15.** Установите collection из локального архива: `ansible-galaxy collection install <archivename>.tar.gz`.
![рисунок 9](https://github.com/ysatii/ansible-hw6/blob/main/img/img9.jpg)


**Шаг 16.** Запустите playbook, убедитесь, что он работает.

ansible-playbook site.yml
ansible-playbook site.yml  # второй раз — должно быть changed=0

 (опционально) переопределить значения:
ansible-playbook site.yml -e "path=/tmp/file_from_installed_role.txt content='Hi from installed collection!'"
![рисунок 10](https://github.com/ysatii/ansible-hw6/blob/main/img/img10.jpg)

**Шаг 17.** В ответ необходимо прислать ссылки на collection и tar.gz архив, а также скриншоты выполнения пунктов 4, 6, 15 и 16.

сама коллекция выставлена в виде отдельного репозитория https://github.com/ysatii/my_own_collection/tree/master 
сам тегг 1.0.0 https://github.com/ysatii/my_own_collection/tags  - 
ссылка  на архив с ролью https://github.com/ysatii/ansible-hw6/blob/main/my_own_namespace-yandex_cloud_elk-1.0.0.tar.gz
ссылка на пайплан с использованием роли https://github.com/ysatii/ansible-hw6/blob/main/site.yml  