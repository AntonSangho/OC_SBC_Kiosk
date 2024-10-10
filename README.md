# 키오스크만들기
![kiosk](https://www.raspberrypi.com/tutorials/tutorials/images/KIOSK-800x533.jpg)

- 키오스크는 사용자에게 특정 정보나 특정 경험을 제공하도록 설계된 반면, 기기의 다른 활동에 대한 액세스는 차단합니다. 
- 공항, 상점, 병원, 카페, 박물관 등 사람들이 시간표, 대기 시간, 제품 정보, 길 찾기, 셀프 체크인 기계 등과 같은 정보나 서비스에 쉽게 접근할 수 있어야 하는 모든 장소에서 흔히 볼 수 있습니다. 
- 라즈베리파이의 키오스크 모드를 사용하면 데스크톱 환경을 사용하지 않고도 전체 화면 웹 페이지나 애플리케이션으로 바로 부팅할 수 있습니다. 사용자와의 전용 상호작용을 위해 정보를 표시하려는 다양한 프로젝트의 기반이 됩니다.
- 키오스크 모드를 시연하기 위해 Raspberry Pi가 전체 화면의 gongdo.kr 웹 페이지로 자동 부팅되도록 설정하고, 이 웹 페이지는 time.is/Seoul 웹 페이지와 함께 회전하도록 하겠습니다.

# 준비물
- 라즈베리파이 4 또는 5
- 라즈베리파이 전원 어댑터
- 마이크로 SD 카드
- 마이크로 SD 카드 리더기
- micro HDMI 케이블
- 모니터
- 마우스 
- 키보드 

# 설치
1. wtype 설치
```bash
sudo apt install wtype 
```
2. 부팅시 로그인 설정 
```bash
sudo raspi-config
```
- System Options -> Boot / Auto Login -> Desktop Autologin : Desktop GUI, automatically logged in as 'pi' user
- Enter -> Finish -> Yes -> Reboot
- 재부팅 후 자동 로그인 확인
3. 부팅시 브라우저 실행
```bash
sudo nano .config/wayfire.ini
```
- 아래 내용을 맨 아래에 추가
```bash
[autostart]
panel = wfrespawn wf-panel-pi
background = wfrespawn pcmanfm --desktop --profile LXDE-pi
xdg-autostart = lxsession-xdg-autostart
chromium = chromium-browser https://gongdo.kr https://time.is/Seoul --kiosk --noerrdialogs --disable-infobars --no-first-run --ozone-platform=wayland --enable-features=OverlayScrollbar --start-maximized
switchtab = bash ~/switchtab.sh
screensaver = false
dpms = false
```
- Ctrl + X -> Y -> Enter
4. 화면 변환하는 스크립트 추가
```bash
nano switchtab.sh
```
- 아래 내용 추가
```bash
#!/bin/bash

# Find Chromium browser process ID
chromium_pid=$(pgrep chromium | head -1)

# Check if Chromium is running
while
[
[ -z $chromium_pid ]]; do
  echo "Chromium browser is not running yet."
  sleep 5
  chromium_pid=$(pgrep chromium | head -1)
done

echo "Chromium browser process ID: $chromium_pid"

export XDG_RUNTIME_DIR=/run/user/1000

# Loop to send keyboard events
while true; do
  # Send Ctrl+Tab using `wtype` command
  wtype -M ctrl -P Tab

  # Send Ctrl+Tab using `wtype` command
  wtype -m ctrl -p Tab

  sleep 10
done
```
- Ctrl + X -> Y -> Enter
- 재부팅 




# 참고 
- [How to use a Raspberry Pi in kiosk mode](https://www.raspberrypi.com/tutorials/how-to-use-a-raspberry-pi-in-kiosk-mode/)
