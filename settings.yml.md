**DO NOT EDIT THIS** wiki entry, it has been moved to the docs ... follow PR https://github.com/asciimoo/searx/issues/1785

This page describe the options possibilities of the settings.yml file.

### Global structure
```YML
server:
    port : 8888
    secret_key : "ultrasecretkey" # change this!
    debug : False # Debug mode, only for development
    request_timeout : 2.0 # seconds
    base_url : False # Set custom base_url. Possible values: False or "https://your.custom.host/location/"
    themes_path : "" # Custom ui themes path - leave it blank if you didn't change
    default_theme : oscar # ui theme
    useragent_suffix : "" # suffix of searx_useragent, could contain informations like an email address to the administrator
    image_proxy : False # Proxying image results through searx
    default_locale : "" # Default interface locale - leave blank to detect from browser information or use codes from the 'locales' config section

# uncomment below section if you want to use a proxy
# see http://docs.python-requests.org/en/latest/user/advanced/#proxies
# SOCKS proxies are not supported : see https://github.com/kennethreitz/requests/pull/478
#outgoing_proxies :
#    http : http://127.0.0.1:8080
#    https: http://127.0.0.1:8080

# uncomment below section only if you have more than one network interface
# which can be the source of outgoing search requests
#source_ips:
#  - 1.1.1.1
#  - 1.1.1.2

locales:
    en : English
    de : Deutsch
    he : Hebrew
    hu : Magyar
    fr : Français
    es : Español
    it : Italiano
    nl : Nederlands
    ja : 日本語 (Japanese)
    tr : Türkçe
    ru : Russian
    ro : Romanian
```

- port : port number of the searx web application if you run it directly using `python searx/webapp.py`. Doesn't apply to searx running on Apache or Nginx.
- secret_key : used for cryptography purpose.
- debug : allow a more detailed log if you run searx directly. Display *detailed* error messages in the browser too, so this must be deactivated in production.
- request_timeout : global timeout of the requests made to others engines in seconds. A bigger timeout will allow to wait for answers from slow engines, but in consequence will slow searx reactivity (the result page may take the time specified in the timeout to load)
- base_url : the base URL where searx is deployed. Used to create correct inbound links.
- themes_path : path to where the themes are located. If you didn't develop anything, leave it blank.
- default_theme : name of the theme you want to use by default on you searx instance.
- useragent_suffix : suffix to the user-agent searx uses to send requests to others engines. If an engine wish to block you, a contact info here may be useful to avoid that.
- image_proxy : allow your instance of searx of being able to proxy images. Uses memory space.
- default_locale : searx interface language. If blank, the locale is detected by using the browser language. If it doesn't work, or you are deploying a language specific instance of searx, a locale can be defined using an ISO language code, like fr, en, de.
- outgoing_proxies : define a proxy you wish to use.
- source_ips : if you use multiple nework interfaces, define from which IP the requests must be made.
- locales : locales codes and their names. Available translations of searx interface.

### Engine basic structure
```YML
- name : bing
  engine : bing
  shortcut : bi
```

- name : name that will be used accross searx to define this engine. In settings, on the result page...
- engine : name of the python file used to handle requests and responses to and from this search engine.
- shortcut : code used to execute bang requests (in this case using !bi or ?bi)

To those basic informations, you can add a few more :
```YML
  base_url : 'https://{language}.wikipedia.org/'
  categories : general
  timeout : 3.0
  api_key : 'apikey'
  disabled : True
  language : en_US
```

- base_url : part of the URL that should be stable accross every request. Can be useful to use multiple sites using only one engine, or updating the site URL without touching at the code.
- categories : define in which categories this engine will be active. Most of the time, it is defined in the code of the engine, but in a few cases it is useful, like when describing multiple search engine using the same code.
- timeout : timeout of the search with the current search engine. Be careful, it will modify the global timeout of searx.
- api_key : in a few cases, using an API needs the use of a secret key. How to obtain them is described in the file.
- disabled : to disable by default the engine, but not deleting it. It will allow the user to manually activate it in the settings.
- language : if you want to use another language for a specific engine, you can define it by using the full ISO code of language and country, like fr_FR, en_US, de_DE.

A few more options are possible, but they are pretty specific to some engines, and so won't be described here.