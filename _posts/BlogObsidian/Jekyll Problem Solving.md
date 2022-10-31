# Invalid date. File does not have a valid date in the YAML front matter.

### Solution Tested (Failed): Vendor

If you install gems with bundle using `--path` flag you need to exclude `vendor` file, so you just need to add `vendor` in the `_config.yml` file.

```makefile
title: Lorem ipsum
author: Lorem
...
exclude:
  - Gemfile
  - Gemfile.lock
  - vendor
```