# Build Debian package Ansible role

Ansible role for building Debian packages from local files.
Supports configuration of control fields via a dict var.

Role Variables
--------------
```yaml
build_debian_package_local_directory: build

# Used with synchronize module. All files within the specified directory
# will be copied into the build directory for packaging. Filetrees are preserved.
# Directories will be copied and merged in the order they are declared.
# Format should be a list of dicts with "src" and "dest" attributes.
# Set "dest" to an empty string to add the files to the base build directory.
build_debian_package_files_directories: []

# Simple files to to copy. Again, use a dict with "src" and "dest" attributes.
build_debian_package_extra_files: []

build_debian_package_base_dependencies:
  - build-essential
  - gcc-4.8
  - make

# List of filepaths that will be removed from the package tree prior to building.
# It's safer to remove as a separate task than to use an rsync filter.
build_debian_package_unwanted_files: []

# List of system packages to install on the build host prior to building.
build_debian_package_extra_dependencies: []

# DEBIAN/control fields for package. Some are required, some recommended.
# See here for details: https://www.debian.org/doc/debian-policy/ch-controlfields.html
# Note that several fields such as Conflicts, Depends, and Removes are expected
# to be lists, rather than strings. See `vars/main.yml` for more info.
build_debian_package_control_fields:
  Package: dummy-package
  Version: 0.1
  Priority: optional
  Architecture: amd64
  Maintainer: SomeOrganization
  Description: This is an empty Debian package.

# Build directories.
build_debian_package_directory: /tmp/{{ build_debian_package_control_fields.Package }}-{{ build_debian_package_control_fields.Version }}-{{ build_debian_package_control_fields.Architecture }}
build_debian_package_deb_file: "{{ build_debian_package_directory }}.deb"

# Support for pip wheel archives. Skipped by default. Paths should be relative
# to the Debian package files, and will be concatenated with the build directory.
# Both vars must be defined.
build_debian_package_pip_requirements: ""
build_debian_package_pip_wheel_directory: ""

# Force ownership of files prior to building. Passed in as rsync flags
# to the `synchronize` module. Setting "root" as default, with the assumption
# of system packages. Chmod options to rsync also force D755 and Fo-w
build_debian_package_owner_username: root
build_debian_package_group_username: root
```

Example Playbook
----------------

```
- name: Build Debian package.
  hosts: buildserver
  become: yes
  roles:
    - role: freedomofpress.build-debian-package
  tags: build
```

Further Reading
---------------
The following resources were invaluable in creating this role.

* [Debian Policy Manual, Chapter 5 - Control files and their fields](https://www.debian.org/doc/debian-policy/ch-controlfields.html)

License
-------

MIT
