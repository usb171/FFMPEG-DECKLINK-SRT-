# Compilando e instalando `ffmpeg` com `Decklink SDK` e o protocolo `SRT`

## Ambiente Linux

- Faça download do `Descktop Video Driver` e `Desktop Video SDK` https://www.blackmagicdesign.com/support/family/capture-and-playback 

- Instale driver baixados 

``` bash
tar -xf Blackmagic_Desktop_Video_Linux_*.tar
cd Blackmagic_Desktop_Video_Linux_*/deb/x86_64/
sudo dpkg -i desktopvideo_*.deb
sudo apt-get install -f
BlackmagicFirmwareUpdater status
# if prompted to update, perform an update:
BlackmagicFirmwareUpdater update 0
# if prompted to shutdown, reboot the device:
sudo reboot
```
