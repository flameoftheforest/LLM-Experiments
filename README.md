# How to setup the LLM dev environment in W11 Pro WSL2
+ Manual sequential steps to set up the development environment in W11 Pro to experiment on LLM and transformer neural net
+ Aggregates from various sources online to 1 place to eliminate the need to hop around for solutions in various steps
+ Can be translated to a dockerFile
+ Most importantly, it's a documentation for me to re-setup the rig easily 😅

## Hardware/OS
+ Intel i5 13400
+ nVidia RTX4060
+ RAM 32GB
+ Win 11 Pro

## Setup
#### 1. WSL
+ In Windows Features, enable:
  + Virtual Machine Platform
  + Windows Subsystem for Linux
+ Run Terminal > Powershell as **Administrator**
+ Run `wsl --list --online` to find a Linux Distro
+ Run `wsl --install -d Ubuntu` to install
+ Follow https://learn.microsoft.com/en-us/windows/wsl/setup/environment
+ (Optional) follow https://learn.microsoft.com/en-us/windows/wsl/install for other details
+ Run `wsl --update`
+ Create a shortcut for Terminal on Desktop
+ Set the shortcut to Run As Administrator
+ Find a nice `.ico` from internet, download and assign to shortcut
+ Pin shortcut to Taskbar
+ Close Terminal, WSL2 should have created a tab for distro
+ If Terminal did not automatically create a profile for WSL, add the profile with:
  + command: `wsl -d Ubuntu`
  + run profile as administrator to true
+ Vanity: 
  + Change name of distro: https://superuser.com/a/1613251
  + Change icon of distro profile: 
    + https://dev.to/thefern/windows-terminal-icons-package-3hid
    + https://github.com/TheFern2/windows-terminal-icons
  + Find and install fonts (probably `NerdFonts.com`)
+ Run `sudo apt-get install wget`

#### 2. ZSH + OMZ
+ Follow https://phoenixnap.com/kb/install-zsh-ubuntu
+ Run `sudo apt upgrade && sudo apt-get update && apt install zsh` 
+ Run `zsh` to create a `.zshrc`
+ Run `chsh -s $(which zsh)` - use ZSH instead BASH, so `.zshrc` will be used instead
+ Run `sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`
+ Modify `.zshrc` to choose omz theme, etc. (follow above link)

#### 3. PowerLevel9K
+ In Windows, download https://github.com/powerline/fonts/blob/master/DejaVuSansMono/DejaVu%20Sans%20Mono%20for%20Powerline.ttf (or something else from this github repo) and add (by double-clicking) to Windows
+ Set font into Terminal's distro's profile
+ Run `git clone https://github.com/bhilburn/powerlevel9k.git ~/.oh-my-zsh/custom/themes/powerlevel9k` (git is already installed for WSL)
+ In `.zshrc`, 
  + add `ZSH_THEME="powerlevel9k/powerlevel9k"`
  + add `POWERLEVEL9K_PROMPT_ON_NEWLINE=true`
  + add `POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(dir vcs)`

#### 4. Miniconda
+ Follow https://docs.conda.io/projects/miniconda/en/latest/index.html for **Miniconda installation**
+ **NOTE**: just need to init zsh using `~/miniconda3/bin/conda init zsh`
+ Integration with OMZ:
  + Run `conda config --set changeps1 false`
  + Add https://github.com/KellieOwczarczak/conda.plugin.zsh to OMZ
  + In `.zshrc`, add `conda` into zsh plugin line
  + Add `conda` into `POWERLEVEL9K_LEFT_PROMPT_ELEMENTS`
  + Modify `conda.plugin.zsh` to include `function prompt_conda()`
+ Run `conda create --name <NAME> python=3.5`
+ Run `source ~/.zshrc` or relaunch WSL/Ubuntu tab and check if env shows up properly
+ Optional but recommended:
  + Create a .sh to `conda activate <ENV_NAME>` and `cd <PROJECT_DIR>`
  + In `.zshrc`, append `source <activate.sh>` 

#### 5. Cuda Driver
+ Follow https://docs.nvidia.com/cuda/wsl-user-guide/index.html#cuda-support-for-wsl-2 for **Cuda driver installation**
+ Follow https://help.totalview.io/previous_releases/2020/HTML/index.html#page/TotalView/totalviewlhug-about-cuda.20.3.html
+ Find Cuda `nvcc` (hint: it should be in `/usr/local/cuda/bin`)
+ Ensure `./zshrc` contains
  ```
  export PATH="/usr/local/cuda/bin:$PATH"
  export LD_LIBRARY_PATH="/usr/local/cuda/lib64:$LD_LIBRARY_PATH"
  export LLAMA_CUBLAS=1 # for llama-cpp-python
  ```
#### 6. "Basic" Python Modules For LLM
+ Following https://colab.research.google.com/github/sophiamyang/demos/blob/main/advanced_rag_small_to_big.ipynb#scrollTo=h0L1ZfgxqPNf
+ Run `pip install -U llama_hub llama_index braintrust autoevals pypdf pillow transformers torch torchvision`
+ `llama_hub` - simple library of all the data loaders / readers / tools that have been created by the community
+ `llama_index` - framework for RAG
+ `braintrust, autoevals` - evaluates response of LLM queries
+ `pypdf` - free and open-source pure-python PDF library capable of splitting, merging, cropping, and transforming the pages of PDF files. It can also add custom data, viewing options, and passwords to PDF files. It can retrieve text and metadata from PDFs as well
+ `pillow` - Adds image processing capabilities to your Python interpreter. This library provides extensive file format support, an efficient internal representation, and fairly powerful image processing capabilities
+ `transformers` - Provides APIs to quickly download and use those pretrained models on a given text, fine-tune them on your own datasets and then share them with the community on our model hub. At the same time, each python module defining an architecture is fully standalone and can be modified to enable quick research experiments. Backed by the three most popular deep learning libraries — Jax, PyTorch and TensorFlow — with a seamless integration between them. It's straightforward to train your models with one before loading them for inference with the other.
+ `torch` - Provides tensor computation (like NumPy) with strong GPU acceleration + deep neural networks built on a tape-based autograd system
+ `torchvision` - Package consists of popular datasets, model architectures, and common image transformations for computer vision

#### 7. Cuda Toolchain