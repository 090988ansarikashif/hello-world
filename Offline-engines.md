This issue is a specification for adding offline engines to searx. Please open an issue on Github if you have any ideas or enhancement requests.

#### What is an offline engine?

An offline engine is an engine which does not need Internet connection to perform a search and does not use HTTP to communicate.

#### Security 

To limit the access to offline engines, if an instance is available publicly, administrators can set token(s) for each private engine. If a query contains a valid token, then searx performs the requested private search. If not, requests from an offline engines return errors.

#### User interface

Offline engines are listed in the available engines list under the appropriate category.

If an engine has advanced settings, it can be configured after clicking on "Advanced settings". 

#### Administering offline engines

If an offline engine depends on an external tool, searx does not install it by default. When an administrator configures such engine and starts searx, the process returns an error with the list of missing dependencies. Also, required dependencies will be added to the comment/description of the engine, so admins can install packages in advance.

If an offline engine has to be private, administrators can provide tokens for the authorized users. When a token is sent to searx with a query to an offline engine, search is performed as described in the section "Security".

#### Programming interface

##### `init()`

All offline engines can have their own `init` function to setup the engine before accepting requests. The function gets the settings from `settings.yml` as a parameter. This function can be omitted, if there is no need to setup anything in advance.

##### `search(query, params)`

Each offline engine has a function named `search`. This function is responsible to perform a search and return the results in a presentable format. (Where presentable means presentable by the selected result template.)

The return value is a list of results retrieved by the engine.

##### `available_params()`

The third function required if the engine has some advanced setting to be configured from the "Advanced settings" section. If the engine does not have such options, return an empty list or omit the function.

#### Engine representation in `/config`

If an engine is offline, the attribute `offline` is set to `True`.