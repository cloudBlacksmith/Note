##### 配置pyenv

环境配置：
```bash
$ sudo dnf install gcc zlib-devel bzip2 bzip2-devel readline-devel sqlite \
sqlite-devel openssl-devel xz xz-devel libffi-devel
$ curl https://pyenv.run | bash
```
运行成功：
```bash
WARNING: seems you still have not added 'pyenv' to the load path.

# Load pyenv automatically by appending
# the following to 
# ~/.bash_profile if it exists, otherwise ~/.profile (for login shells)
# and ~/.bashrc (for interactive shells) :

export PYENV_ROOT="$HOME/.pyenv"
[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"

# Restart your shell for the changes to take effect.

# Load pyenv-virtualenv automatically by adding
# the following to ~/.bashrc:

eval "$(pyenv virtualenv-init -)"
```

安装python
```bash
pyenv install -v "python版本" 
```

为目录配置python
```bash
pyenv local "python版本" 
```
用户级配置
```bash
pyenv global "python版本" 
```
#### 查看当前是否为虚拟环境
```bash
which python
python -m venv myenv```
#### 创建虚拟环境
```bash
python -m venv myenv
bash
```
#### 激活虚拟环境
```
source myenv/bin/activate
```
#### 退出虚拟环境
```bash
deactivate
```

#### 配置pip源
```bash
cat "[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
extra-index-url =
    https://pypi.org/simple
    https://mirrors.aliyun.com/pypi/simple
timeout = 120" > ~/.config/pip.conf
```