#### logging module
参考https://www.cnblogs.com/yyds/p/6901864.html

```python
import logging
from flask import Flask

app = Flask(__name__)


@app.route("/")
def hello_world():
    app.logger.debug("debug debug")
    app.logger.info("info info")
    app.logger.warning("warn warn")
    app.logger.error("error error")
    return "<p>Hello World!</p>"


if __name__ == '__main__':
    logFormat = "%(asctime)s %(levelname)s: %(message)s"  # 设置日志输出格式
    file_handler = logging.FileHandler("flask.log")
    file_handler.setFormatter(logging.Formatter(logFormat))
    app.logger.setLevel(logging.DEBUG)  # 设置日志级别，大于等于DEBUG级别的日志都输出
    app.logger.addHandler(file_handler)
    app.run("0.0.0.0", 80, debug=True)
```

#### 按时间轮换日志TimedRotatingFileHandler

```python
import logging
from flask import Flask
from logging.handlers import TimedRotatingFileHandler
app = Flask(__name__)


@app.route("/")
def hello_world():
    # app.logger.exception("error error")
    app.logger.debug("debug debug")
    app.logger.info("info info")
    app.logger.warning("warn warn")
    app.logger.error("error error")
    # logging.info("logging info ... ")
    # logging.warning("logging warning ...")
    # logging.error("logging error ... ")
    try:
        raise NameError
    except:
      # logging.exception可以输出异常的详细信息，包括错误类型等，logging.error只输出自定义的错误信息
        app.logger.exception("logging exception ... ") # 最好配合try..except使用，捕获异常信息，单独使用会报NoneType,获取不到异常类型（默认获取异常信息exc_info=True,设置为false应该就可以了，但是感觉时区了意义，和error没啥区别)

    return "<p>Hello World!</p>"


if __name__ == '__main__':
    logFormat = "%(asctime)s %(levelname)s: %(message)s"
    # logger = logging.getLogger()
    # file_handler = logging.FileHandler("flask.log")
    rotate_handler = TimedRotatingFileHandler(filename="flask.log", when="H", interval=2, backupCount=5)
    rotate_handler.setFormatter(logging.Formatter(logFormat))
    app.logger.setLevel(logging.DEBUG)
    app.logger.addHandler(rotate_handler)
    # logging.basicConfig(filename="flask.log", format=logFormat, level=logging.INFO)
    app.run("0.0.0.0", 80, debug=True)
```

#### 按文件大小轮换日志，并同时输出到终端

```python
import logging
import sys
from flask import Flask
from logging.handlers import TimedRotatingFileHandler, RotatingFileHandler

app = Flask(__name__)


@app.route("/")
def hello_world():
    # app.logger.exception("error error")
    app.logger.debug("debug debug")
    app.logger.info("info info")
    app.logger.warning("warn warn")
    app.logger.error("error error")
    # logging.info("logging info ... ")
    # logging.warning("logging warning ...")
    # logging.error("logging error ... ")
    try:
        raise NameError
    except:
        app.logger.exception("logging exception ... ")

    return "<p>Hello World!</p>"


if __name__ == '__main__':
    logFormat = "%(asctime)s %(levelname)s: %(message)s"
    # logger = logging.getLogger()
    # file_handler = logging.FileHandler("flask.log")
    # rotate_handler = TimedRotatingFileHandler(filename="flask.log", when="S", interval=2, backupCount=5)
    # 设置按文件大小轮转的handler
    rotate_handler = RotatingFileHandler(filename="flask.log", mode="a", maxBytes=102400, backupCount=3,
                                         encoding="UTF-8", delay=False)
    rotate_handler.setFormatter(logging.Formatter(logFormat))

    # 设置控制台输出的handler，默认输出到sys.stderr
    stream_handler = logging.StreamHandler()
    stream_handler.setFormatter(logging.Formatter(logFormat))

    # 设置日志最小输出级别
    app.logger.setLevel(logging.DEBUG)

    # 将日志输出到handler
    app.logger.addHandler(rotate_handler)
    app.logger.addHandler(stream_handler)
    # logging.basicConfig(filename="flask.log", format=logFormat, level=logging.INFO)
    app.run("0.0.0.0", 80, debug=True)
```
