+++
title = "z790-dasharo-cont"
date = "2026-02-17"

[taxonomies]
tags = ["tag1", "tag2"]
+++

# z790-dasharo-continued...ollama and gpu passthru
 ## 12:57
so starting with the default install of arch using archinstall, i made my own trouble getting the nvidia passthru to work.

namely, the archinstaller allows you to select the drivers and even though i sort-of recalled runnning across a github dasharo issue on this i went with the default open drivers for nvidia during the setup.

now, for anyone including me who may deal with this again...if you make the same mistake, there's nothing indicating that its not working correctly.  nvidia-smi works on host and with docker exec, it just doesn't with cuda. i tried everything. podman, docker uninstall, reinstall etc. literally a full day trying to bring back my ollama and open-webui instance since id replaced the board. of course this matters beyond my own little application, but there ya go.

## fixes

so after some time i finally came upon this dasharo issue that pointed me in the right direction:
<https://github.com/Dasharo/dasharo-issues/issues/1135>

now according to this, and the issue being corrected with a work-around, i dont think dasharo will be addressing this with their bios upgrades...

so you have to reinstall nvidia drivers...here's what i did to get that going

```
sudo pacman -S nvidia nvidia-settings nvidia-utils cuda
```

i am running cachyos on my laptop, so it looks a little different, but it should show a couple repo options as well as chaotic-AUR (if you have that), and i just hit enter...this should also show you a dependent dkms which you want as well. mine were all of the 580xx variety which is what nvidia stopped propreitary drivers at...this was replacing my 590xx drivers, and when prompted agree to that.

next i followed along on my jellyfin tutorial to set those up, handy since the other inscance i used this for needed the same drivers.

<https://inktheghost.com/blog/jellyfin/>
### Installing Open WebUI with Bundled Ollama Support

[](https://github.com/open-webui/open-webui?tab=readme-ov-file#installing-open-webui-with-bundled-ollama-support)

This installation method uses a single container image that bundles Open WebUI with Ollama, allowing for a streamlined setup via a single command. Choose the appropriate command based on your hardware setup:

install docker first:
```shell
sudo pacman -S docker docker-compose
sudo systemctl enable --now docker
sudo systemctl start docker
```

then install more nvidia stuff and setup configs:

```shell
paru -S nvidia-container-toolkit
sudo nvidia-ctk cdi generate --output=/etc/cdi/nvidia.yaml
sudo nvidia-ctk config --in-place --set nvidia-container-runtime.mode=cdi
sudo systemctl daemon-reload
sudo systemctl restart docker
```

i also needed to add my user to docker so i could issue future docker commands without sudo and setup the docker runtime

```Shell
sudo usermod -aG docker $USER   
sudo nvidia-ctk runtime configure --runtime=docker
```

i struggled with many different versions of the docker-compose method vs cli and finally settled on the simple solution...duh, found on the open-webui github
<https://github.com/open-webui/open-webui#troubleshooting>

### Installing Open WebUI with Bundled Ollama Support

[](https://github.com/open-webui/open-webui?tab=readme-ov-file#installing-open-webui-with-bundled-ollama-support)

This installation method uses a single container image that bundles Open WebUI with Ollama, allowing for a streamlined setup via a single command. Choose the appropriate command based on your hardware setup:

With GPU Support**: Utilize GPU resources by running the following command:
```shell
docker run -d -p 3000:8080 --gpus=all -v ollama:/root/.ollama -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:ollama
```

that came up after a couple minutes and i was able to access off mu 