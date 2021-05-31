# **VSCode 免密碼登入**

#自動登入(vscode)

Host 120.96.143.166
    HostName 120.96.143.166
    User bigred
    IdentityFile C:\\Users\\delta\\.ssh\\id_rsa

Host 120.96.143.167
    HostName 120.96.143.167
    User bigred
    IdentityFile C:\\Users\\delta\\.ssh\\id_rsa

Host 120.96.143.168
    HostName 120.96.143.168
    User bigred
    IdentityFile C:\\Users\\delta\\.ssh\\id_rsa

 Host 120.96.143.169
    HostName 120.96.143.169
    User bigred
    IdentityFile C:\\Users\\delta\\.ssh\\id_rsa

#進powershell介面->
ssh-keygen -t rsa -b 4096


#C:\Users\delta\.ssh (到.ssh目錄底下)

`$USER_AT_HOST="bigred@120.96.143.166"`
`$PUBKEYPATH="$HOME\.ssh\id_rsa.pub"`

`$pubKey=(Get-Content "$PUBKEYPATH" | Out-String); ssh "$USER_AT_HOST" "mkdir -p ~/.ssh && chmod 700 ~/.ssh && echo '${pubKey}' >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"`