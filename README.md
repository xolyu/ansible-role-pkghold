# pkghold &ndash; Package Hold

<!-- [![CI](https://github.com/xolyu/ansible-role-pkghold/actions/workflows/ci.yml/badge.svg)](https://github.com/xolyu/ansible-role-pkghold/actions/workflows/ci.yml) -->

Sets the status of installed packages to `hold` to prevent the packages from being updated or to `unhold` to allow the packages to be updated again. Installed packages can be identified by name or origin.

This role only works on Debian-based systems with the package manager apt.


## Requirements

None.


## Dependencies

None.


## Role Variables

* **`pkghold_force_regather_facts`**  
  Defines whether `package_facts` should be executed again, even if `ansible_facts.packages` has already been initialized.  
  Type: bool  
  Default: `no`

* **`pkghold_unhold_also_uninstalled`**  
  Defines whether an `unhold` should also be executed for packages that are not installed. This only works if the package is defined directly by name (string entry in `pkghold_pkgs`).  
  Type: bool  
  Default: `yes`

* **`pkghold_pkgs`**  
  List of packages to be set to hold or unhold according to `pkghold_state`.  
  List entries can be strings with the package name, or a dict as `{"origin": "<ORIGIN_NAME>"}`.  
  If a dict is defined with an `origin` key, the installed packages are searched for this origin and these packages are marked accordingly.  
  The list entries can be mixed strings and dicts.  
  Type: list of str or dicts  
  Default: _undefined_

  Example:

  ```yml
  pkghold_pkgs:
    - vim
    - origin: Docker
    - apache2
    - nginx
  ```

* **`pkghold_state`**  
  Marking to be performed for the named packages.  
  Choices: `hold`, `unhold`  
  Default: _undefined_

<!--
* **`VAR`**  
  DESC  
  Type: bool/str/dict/list/list of str/list of dicts/dict of dict  
  Default: `VAL`

* **`VAR`**  
  DESC  
  Choices: `VAL`, `ANOTHER`  
  Default: `VAL`
-->


## Example Playbook

```yml
- name: Hold Packages
  hosts: localhost
  become: true

  roles:
    - role: xolyu.pkghold
      pkghold_state: hold
      pkghold_pkgs:
        - vim
        - apache2
        - nginx
```

If you have installed Docker from the Docker package sources, you can, for example, set all packages with the origin `Docker` to hold.

```yml
- name: Hold Packages
  hosts: localhost
  become: true

  roles:
    - role: xolyu.pkghold
      pkghold_state: hold
      pkghold_pkgs:
        - origin: Docker
```

To update Docker specifically, first perform unhold, then set to hold again after the update. _Attention: This playbook is not idempotent._

```yml
- name: Update Docker
  hosts: localhost
  become: true

  tasks:

    - name: Unhold Docker packages.
      include_role:
        name: pkghold
      vars:
        pkghold_state: unhold
        pkghold_pkgs:
          - origin: Docker

    - debug:
        msg: Perform updates

    - name: Hold Docker packages.
      include_role:
        name: pkghold
      vars:
        pkghold_state: hold
        pkghold_pkgs:
          - origin: Docker
```


## License

GNU General Public License v3.0


## Author Information

Xolyu.
