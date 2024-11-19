## 创建项目

```bash
scrapy startproject <project name>
```

### 项目结构

```
<project name>/
    scrapy.cfg            # deploy configuration file

    <project name>/             # project's Python module, you'll import your code from here
        __init__.py

        items.py          # project items definition file

        middlewares.py    # project middlewares file

        pipelines.py      # project pipelines file

        settings.py       # project settings file

        spiders/          # a directory where you'll later put your spiders
            __init__.py
```