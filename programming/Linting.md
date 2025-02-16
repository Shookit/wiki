Linting
=======

Every repository should have a linter, even plaintext repos!

Python
------

### Pylint

```
# Must use virtualenv's pylint if using one, not the package manager's pylint
pip install pylint

# Until pylint 1.8 is released:
pip install git+ssh://git@github.com/PyCQA/pylint
```

Pylint scans upward in a project to search for a .pylintrc file (otherwise it uses the home directory).

This should work in vim via the ALE plugin, but make sure you set init-hook to your python root's source directory.

```
# .pylintrc
init-hook='import sys; sys.path.append("./src")'
```

```bash
# Create bare pylint rc file
pylint --generate-rcfile > .pylintrc

# Run pylint recursively on all files in a directory
find . -name "*.py" | xargs pylint
```


### Autopep8

```bash
# See available options
autopep8 --list-fixes

# Run fixes (in-place)
autopep8 -r --in-place --max-line-length 50000000000 --select E,W
```


Javascript
----------

eslint scans upward in a project to search for a .eslintrc file (similar to pylint)

This should work in vim via the ALE plugin.

```bash
eslint dir/
eslint dir/file.js
```

Markdown
--------

Ruby must be installed to install gems

```bash
gem install mdl
~/.gem/ruby/2.4.0/bin/mdl -s ./mdlrc .
```

Ansible
-------

```
pip2 install ansible-lint
ansible-lint playbook.yml
ansible-lint roledir
```
