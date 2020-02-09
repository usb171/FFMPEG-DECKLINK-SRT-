<p align="center">
  <a href="http://srtalliance.org/">
    <img alt="SRT" src="http://www.srtalliance.org/wp-content/uploads/SRT_text_hor_logo_grey.png" width="600"/>
  </a>
  <a href="https://www.ffmpeg.org/">
    <img alt="SRT" src="https://avatars2.githubusercontent.com/u/729418?s=200&v=4" width="600"/>
  </a>
</p>


# Compilando e instalando `ffmpeg` com `Decklink SDK` e o protocolo `SRT`

## Ambiente Linux

- Faça download do `Descktop Video Driver` e `Desktop Video SDK` https://www.blackmagicdesign.com/support/family/capture-and-playback 

- Instalando driver Decklink

```bash
tar -xf Blackmagic_Desktop_Video_Linux_*.tar
cd Blackmagic_Desktop_Video_Linux_*/deb/x86_64/
sudo dpkg -i desktopvideo_*.deb
sudo apt-get install -f
BlackmagicFirmwareUpdater status
# se solicitado a atualizar, execute uma atualização:
BlackmagicFirmwareUpdater update 0
# se solicitado a renicialização, reinicie o dispositivo:
sudo reboot
```

- Unzip Desktop Video SDK
```bash
sudo apt install unzip
unzip Blackmagic_DeckLink_SDK_*.zip
mkdir -p ~/ffmpeg_sources ~/bin
cp -r Blackmagic\ DeckLink\ SDK\ */Linux/ ~/ffmpeg_sources/BMD_SDK
```

- Instale o protocolo SRT

```bash
sudo apt-get install libssl-dev
cd ~/ffmpeg_sources
git clone --depth 1 https://github.com/Haivision/srt.git
mkdir srt/build
cd srt/build
cmake -DCMAKE_INSTALL_PREFIX="$HOME/ffmpeg_build" -DENABLE_C_DEPS=ON -DENABLE_SHARED=OFF -DENABLE_STATIC=ON ..
make
make install
```
