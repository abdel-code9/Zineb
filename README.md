name: Free RDP

on: 
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: Install XFCE and xrdp
      run: |
        sudo apt update
        sudo apt install xfce4 xfce4-goodies xrdp -y
        sudo systemctl enable xrdp
        sudo adduser --disabled-password --gecos "" rdpuser
        echo "rdpuser:123456" | sudo chpasswd
        sudo usermod -aG sudo rdpuser
    - name: Install Ngrok
      run: |
        curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null
        echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | sudo tee /etc/apt/sources.list.d/ngrok.list
        sudo apt update && sudo apt install ngrok -y
    - name: Start Ngrok tunnel
      run: |
        ngrok config add-authtoken 2x43YmoBy6YLIXepUBYepB9Fh6l_7aBVKhJTt6ms8YtMg8TK5
        ngrok tcp 3389 > /dev/null &
        sleep 15
        curl -s http://localhost:4040/api/tunnels > tunnels.json
        cat tunnels.json
