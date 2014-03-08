run these commands in the root directory of searx

### Add new language

`pybabel init -i messages.pot -d searx/translations -l it`

### Update .po files

`./utils/update-translations.sh`

### Compile translations

`pybabel compile -d searx/translations`

