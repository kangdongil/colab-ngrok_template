## Colab을 Ngrok을 이용해 Local 환경에서 사용하기
### Google Colab
1. Google Drive 접근권한을 얻는다
    ```python
    # Get Access to Google Drive
    from google.colab import drive
    drive.mount('/content/drive')
    ```
2. Ngrok로부터 AuthToken 값을 받는다
    - 링크: [Ngrok_AuthToken](https://dashboard.ngrok.com/get-started/your-authtoken)
3. `ColabSSH`를 `pip install`하고 Ngrok로부터 받은 `authtoken`과 임의로 정한 `password`(local에서 접속할때 필요하다)을 `launch_ssh`에 넣고 실행한다
    ```python
    !pip install colab-ssh --upgrade

    authtoken = XXXXXXXXXXXX
    password = 0000

    # Launch Ngrok to get Host Information
    from colab_ssh import launch_ssh
    launch_ssh(authtoken, password)
    ```
4. SSH를 성공적으로 실행하면 아래와 같은 host정보를 복사한다
    ```shell
    Host google_colab_ssh
        HostName X.tcp.ngrok.io
        User root
        Port XXXXX
    ```
### Local VSCode
1. `Command Palette`(`Ctrl+Shift+P`)로 원격SSH에 대한 설정을 한다
    1. `Remote-SSH: Open SSH Configuration File...`
        - `Users/USERNAME/.ssh/config` 파일을 접속한다
        - Colab의 SSH정보를 삽입한다
    2. `Remote-SSH: Connect to Host...` - `google_colab_ssh`
        - `continue` - `password` 입력
2. 해당 작업환경이 GPU를 사용할 수 있는지 확인하기 위해, 터미널을 켜고 `nvidia-smi` 명령어를 입력해본다

## 환경변수(Environment Variable) 설정하기
```
AuthToken와 password를 GitHub에 업로드되는 걸 방지하고자 환경변수로 별도 관리한다
```
1. Google Drive에 환경변수 파일을 생성한다
    ```shell
        touch /content/drive/MyDrive/Colab Notebooks/.env
    ```
2. .env파일에 환경변수를 작성한뒤 경로를 얻는다
    ```
    NGROK_AUTH_TOKEN='XXXXXXXXXXXXXXXXXX'
    REMOTE_ACCESS_PASSWORD='0000'
    ```
    ```shell
    /content/drive/MyDrive/Colab Notebooks/.env
    ```
3. `python-dotenv`을 `pip install`하고 해당 ENV파일을 `load_dotenv`해준다
    ```python
    !pip install python-dotenv -qU

    envpath = '/content/drive/MyDrive/Colab Notebooks/.env'
    from dotenv import load_dotenv

    load_dotenv(envpath, override=True)
    ```
4. `os.environ.get`으로 환경변수를 얻는다
    ```python
    import os

    authtoken = os.environ.get('NGROK_AUTH_TOKEN')
    password = os.environ.get('REMOTE_ACCESS_PASSWORD')
    ```