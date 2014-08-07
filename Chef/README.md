Operation team require for every pull request to pass foodcritic test.

Foodcritic disabled tests:
--------------------------
* FC003: Check whether you are running with chef server before using server-specific features (http://www.foodcritic.io/#FC003)
* FC011: Missing README in markdown format (http://www.foodcritic.io/#FC011)
* FC015: Consider converting definition to a LWRP (http://www.foodcritic.io/#FC015)
* FC023: Prefer conditional attributes
* FC040: Execute resource used to run git commands

Example of usage:

```
bundle install --path vendor/bundle
bundle exec foodcritic -f any --tags '~FC003' --tags '~FC015' --tags '~FC023' --tags '~FC011' --tags '~FC040' path_to_cookbook
```
