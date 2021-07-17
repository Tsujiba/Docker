# なぜWindowsやMacのPCでLinuxコンテナが利用できるのか？
Linuxの仮想マシンを内部的に作成している<br>
　　例）WinならHyprer-Vを利用している

# Docker Desktopをインストール
- Winの場合はHomeでもできるが、WSL２をインストールする必要がある⇒更新プログラミング適用する

## 動作確認
- PowerShellで`decker run hello-world`<br>
　└動作確認用のコンテナイメージ