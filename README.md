# 🎬 공학도서관 오리지널 컨텐츠

# 🖥️ 라즈베리파이 광고판 만들기

![kiosk](https://www.raspberrypi.com/tutorials/tutorials/images/KIOSK-800x533.jpg)

## 📝 프로젝트 소개
이 프로젝트는 라즈베리파이를 활용하여 웹 기반 키오스크를 만드는 과정을 설명합니다. 웹페이지가 전체 화면으로 자동 실행되고, 여러 페이지가 자동으로 전환되는 디지털 광고판을 만들어볼 수 있습니다.

## 📚 사전학습
이 프로젝트를 시작하기 전에 아래 내용을 먼저 공부하고 오시면 좋아요.

- 라즈베리파이 기초
  - 라즈베리파이 OS 사용법
  - 터미널 명령어 이해하기
  - 시스템 설정 방법 알아보기

- 리눅스 시스템
  - 기본 파일 편집 방법
  - 시스템 서비스 이해하기
  - 자동 실행 설정 방법

- 웹 브라우저
  - Chromium 브라우저 옵션 이해하기
  - 키오스크 모드 개념
  - 웹 페이지 자동 전환 방식

## 🛠 준비물
- 라즈베리파이 4
- SD 카드
- 모니터
- HDMI 케이블
- 키보드/마우스
- 전원 어댑터

## 💾 실습 코드
### 1. Wayfire 설정 파일 (`/home/pi/.config/wayfire.ini`)
```ini
# 파일 끝에 추가할 내용

[autostart]
panel = wfrespawn wf-panel-pi
background = wfrespawn pcmanfm --desktop --profile LXDE-pi
xdg-autostart = lxsession-xdg-autostart
chromium = chromium-browser https://gongdo.kr https://127.0.0.1:5000 --kiosk --noerrdialogs --disable-infobars --no-first-run --ozone-platform=wayland --enable-features=OverlayScrollbar --start-maximized
switchtab = bash ~/switchtab.sh
screensaver = false
dpms = false
```

### 2. 탭 전환 스크립트 (`~/switchtab.sh`)
```bash
#!/bin/bash

# Find Chromium browser process ID
chromium_pid=$(pgrep chromium | head -1)

# Check if Chromium is running
while [[ -z $chromium_pid ]]; do
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
  wtype -m ctrl -p Tab
  sleep 10
done
```

## 🚀 시작하기
1. wtype 설치하기
   ```bash
   sudo apt-get install wtype
   ```
2. 라즈베리파이 설정하기
   - `sudo raspi-config` 실행
   - "System Options" 선택
   - "Boot / Auto Login" 선택
   - "Desktop Autologin" 선택
   - 재부팅 여부 묻는 창에서 "No" 선택

3. Wayfire 디스플레이 설정하기
   - `sudo raspi-config` 실행
   - "Display" 선택
   - "Wayland" 선택
   - "Wayfire" 선택하고 확인

4. 자동 실행 스크립트 설정하기
   ```bash
   # switchtab.sh 파일 생성
   nano ~/switchtab.sh
   
   # 실행 권한 부여
   chmod +x ~/switchtab.sh
   ```

5. Wayfire 설정 파일 수정하기
   ```bash
   # 설정 파일 열기
   nano ~/.config/wayfire.ini
   
   # 파일 끝에 [autostart] 섹션 추가
   # (실습 코드의 Wayfire 설정 내용 붙여넣기)
   ```

6. Flask 웹페이지 자동 실행 설정
   ```bash
   sudo nano /etc/rc.local
   ```
   - exit 0 전에 다음 내용 추가:
   ```bash
   sudo python3 /home/pi/webapp/app.py
   ```

7. Wayfire 디스플레이 서버 설정
   ```bash
   sudo raspi-config
   ```
   - Advanced Options 선택
   - Wayland 선택
   - Wayfire 선택
   - 재부팅하기

8. 키오스크 모드 테스트 및 관리
   - 재부팅 후 키오스크 모드 동작 확인
   - 웹페이지 자동 전환 확인
   - 전체화면 표시 확인

9. 키오스크 모드 해제하기
   - Ctrl + Alt + T로 터미널 실행
   - Wayfire 설정 파일 열기:
   ```bash
   sudo nano .config/wayfire.ini
   ```
   - [autostart] 섹션 주석 처리:
   ```ini
   #[autostart]
   #panel = wfrespawn wf-panel-pi
   #background = wfrespawn pcmanfm --desktop --profile LXDE-pi
   #xdg-autostart = lxsession-xdg-autostart
   #chromium = chromium-browser https://gongdo.kr https://time.is/Seoul --kiosk --noerrdialogs --disable-infobars --no-first-run --ozone-platform=wayland --enable-features=OverlayScrollbar --start-maximized
   #switchtab = bash ~/switchtab.sh
   #screensaver = false
   #dpms = false
   ```
   - 저장 후 재부팅

## 🔍 문제해결
- 화면이 자동으로 전환되지 않아요
  - switchtab.sh의 실행 권한을 확인해보세요.
  - wtype이 제대로 설치되었는지 확인해보세요.
  - 스크립트의 오타를 확인해보세요.

- 브라우저가 전체 화면으로 실행되지 않아요
  - wayfire.ini의 chromium 설정에 --kiosk 옵션이 있는지 확인해보세요.
  - Wayfire가 기본 디스플레이 서버로 설정되어 있는지 확인해보세요.
  - 재부팅 후 다시 시도해보세요.

- 자동 로그인이 되지 않아요
  - raspi-config에서 자동 로그인 설정을 다시 확인해보세요.
  - 사용자 계정 권한을 확인해보세요.
  - 시스템 업데이트 후 다시 시도해보세요.

## 🌟 이렇게 업그레이드 해볼 수 있어요
- 다양한 웹페이지 표시하기
  - 더 많은 URL을 추가해보세요.
  - 페이지별로 다른 표시 시간을 설정해보세요.
  - 특정 시간에 다른 내용을 보여주도록 만들어보세요.

- 인터랙티브 기능 추가하기
  - 터치스크린 지원을 추가해보세요.
  - 동작 감지 센서를 연결해보세요.
  - 음성 안내 기능을 넣어보세요.

- 관리 시스템 만들기
  - 원격으로 내용을 수정할 수 있게 해보세요.
  - 디스플레이 상태를 모니터링해보세요.
  - 콘텐츠 스케줄링 기능을 추가해보세요.

## 📚 참고 자료
- [라즈베리파이 디스플레이 설정](https://www.raspberrypi.com/documentation/computers/config_txt.html)
- [Chromium 명령줄 스위치](https://peter.sh/experiments/chromium-command-line-switches/)

> **주의사항**: 
> - 시스템 설정을 변경할 때는 백업을 먼저 해두세요.
> - 키오스크 모드에서는 Ctrl+Alt+T로 터미널을 열 수 있어요.
> - 실제 서비스 전에 보안 설정을 꼭 확인하세요.
> - 자동 실행 스크립트의 권한 설정을 잊지 마세요.

이것으로 전체 README가 완성되었습니다. 수정이나 보완이 필요한 부분이 있을까요?

