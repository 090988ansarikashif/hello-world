run these commands in the root directory of searx

### Add new language

`pybabel init -i messages.pot -d searx/translations -l it`

### Update .po files

`./utils/update-translations.sh`

### Compile translations

`pybabel compile -d searx/translations`

# Transifex stuff

### Init Project

```shell
tx set --auto-local -r searx.messagespo 'searx/translations/<lang>/LC_MESSAGES/messages.po' \
--source-lang en --type PO --source-file messages.pot
```
http://docs.transifex.com/developer/client/set

_TODO: mapping between transifex and searx_

### get  Translations

```shell
tx pull -a
```
http://docs.transifex.com/developer/client/pull