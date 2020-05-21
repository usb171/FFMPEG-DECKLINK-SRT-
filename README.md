# Compilando e instalando `ffmpeg` com `Decklink SDK` e o protocolo `SRT`

<p class align="center">
  <a href="https://www.blackmagicdesign.com">
      <img alt="SRT" src="https://upload.wikimedia.org/wikipedia/commons/b/bc/Blackmagic_Design_logo.png" width="400"/>
  </a>
</p>

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

<p align="center">
  <a href="http://srtalliance.org/">
    <img alt="SRT" src="http://www.srtalliance.org/wp-content/uploads/SRT_text_hor_logo_grey.png" width="400"/>
  </a>
</p>

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
<p align="center">
  <a href="https://www.ffmpeg.org/">
    <img alt="SRT" src="https://upload.wikimedia.org/wikipedia/commons/5/5f/FFmpeg_Logo_new.svg" width="400"/>
  </a>
</p>

- Siga as instruções para compilar o `ffmpeg` https://trac.ffmpeg.org/wiki/CompilationGuide/Ubuntu
  - Install dependencies
    ```bash
    sudo apt-get update -qq && sudo apt-get -y install \
      autoconf \
      automake \
      build-essential \
      cmake \
      git-core \
      libass-dev \
      libfreetype6-dev \
      libtool \
      libvorbis-dev \
      pkg-config \
      texinfo \
      wget \
      zlib1g-dev
    sudo apt-get -y install \
      nasm yasm libx264-dev libx265-dev libnuma-dev libvpx-dev \
      libfdk-aac-dev libmp3lame-dev libopus-dev
    ```
  - Baixe o código fonte do `ffmpeg` e siga os comandos abaixo.
    ```bash
    cd ~/ffmpeg_sources
    wget https://ffmpeg.org/releases/ffmpeg-4.1.3.tar.bz2
    tar -xvf ffmpeg-*.tar.bz2
    cd ffmpeg-*/
    PATH="$HOME/bin:$PATH" PKG_CONFIG_PATH="$HOME/ffmpeg_build/lib/pkgconfig" ./configure \
      --prefix="$HOME/ffmpeg_build" \
      --pkg-config-flags="--static" \
      --extra-cflags="-I$HOME/ffmpeg_build/include -I$HOME/ffmpeg_sources/BMD_SDK/include" \
      --extra-ldflags="-L$HOME/ffmpeg_build/lib" \
      --extra-libs="-lpthread -lm" \
      --bindir="$HOME/bin" \
      --enable-gpl \
      --enable-libass \
      --enable-libfdk-aac \
      --enable-libfreetype \
      --enable-libmp3lame \
      --enable-libopus \
      --enable-libvorbis \
      --enable-libvpx \
      --enable-libx264 \
      --enable-libx265 \
      --enable-nonfree \
      --enable-decklink \
      --enable-libsrt
    ```
    Observe a adição de `--enable-decklink`, `--enable-libsrt` e BlackMagicDesign SDK em` --extra-cflags`. Existem alterações de API no BMD SDK 11 em diante; portanto, se o ffmpeg não for compilado, compile-o com o BMD SDK 10.11.4
  - Compile e instale.
    ```bash
    PATH="$HOME/bin:$PATH" make -j `nproc`
    sudo cp ffmpeg ffprobe /usr/local/bin/
    ```
  - Essa documentação teve como base os seguintes projetos
    https://github.com/Haivision/srt/blob/master/README.md
    https://gist.github.com/afriza/879fed4ede539a5a6501e0f046f71463
    
    
- Exemplo de execução

  - Server SRT
  ```
  [program:SRT1]
  command=srt-live-transmit srt://5000 srt://9999
  user=cidadeverde
  numprocs=1
  autostart=true
  autorestart=true
  startsecs=10
  startretries=1000
  priority=1
  ```

  - SRT -> BlackMagic
  ```ffmpeg -probesize 1024 -vcodec hevc -loglevel debug -i srt://192.168.254.70:9998 -f decklink -pix_fmt uyvy422 -         format_code Hi59 -s 1920x1080 -r 30000/1001 'DeckLink Duo (2)' -analyzeduration 0 ```


    
 
