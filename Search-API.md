#### Search API endpoints: `/`, `/search`

Endpoints have equivalent functionality.


#### Parameters

| Name         | Description                                                                                   |                         |
| ------------ | --------------------------------------------------------------------------------------------- | ----------------------- |
| `q`          | The search query, see [Query language](https://github.com/asciimoo/searx/wiki/Query-language) | required                |
| `categories` | Comma separated list, specifies the active search categories                                  | optional                |
| `engines`    | Comma separated list, specifies the active search engines                                     | optional                |
| `pageno`     | Search page number                                                                            | optional (default: `1`) |

Both `GET` and `POST` methods are supported.
