# Reveal password encrypted by MobaXterm

说明：使用大佬 https://github.com/HyperSine/how-does-MobaXterm-encrypt-password 的源进行的改进。
由于无法上传新的MobaXtermCipher_fixed.py文件，所以转移到我这里，如果有问题可以随时联系我。

* 我进行验证的环境是 MacBookPro M1 


MobaXtermCipher_fixed.py
文件主要变更：将原作者脚本中的 ansi 编码替换为 utf-8



## 1. How does it work?

See [here](doc/how-does-mobaxterm-encrypt-password.md)


## 2. How to use?

* Make sure you have Python3 and have `pycryptodome` installed.

```
Usage:
    MobaXtermCipher.py <enc|dec> [-sysh sys_hostname] [-sysu sys_username]
                                 <-h conn_hostname> <-u conn_username>
                                 <plaintext|ciphertext>

    MobaXtermCipher.py <enc|dec> <-sp SessionP> <plaintext|ciphertext>

    MobaXtermCipher.py <enc|dec> <-p master_password> <plaintext|ciphertext>

        <enc|dec>                "enc" for encryption, "dec" for decryption.
                                 This parameter must be specified.

        [-sysh sys_hostname]     Hostname of system where MobaXterm runs.
                                 This parameter is optional. If not specified, use current system hostname.

        [-sysu sys_username]     Username of system where MobaXterm runs.
                                 This parameter is optional. If not specified, use current system username.

        <-h conn_hostname>       Hostname of MobaXterm connection config.
                                 This parameter must be specified.

        <-u conn_username>       Username of MobaXterm connection config.
                                 This parameter must be specified.

        <-sp SessionP>           The value `SessionP` stored in key HKCU\Software\Mobatek\MobaXterm
                                 This parameter must be specified.

        <-p master_password>     The master password set in MobaXterm.
                                 This parameter must be specified.

        <plaintext|ciphertext>   Plaintext string or ciphertext string.
                                 This parameter must be specified.
```

```
Usage:
    ShowMobaXterm.py [master_password]

        [master_password]        The master password set in MobaXterm.
                                 This parameter is optional, 
                                 but must be specified if you set a master password in MobaXterm.
```

## 3. Example（Windows）:

MobaXterm will save passwords and credentials in:

|Type       |Registry Path                      |
|-----------|-----------------------------------|
|Credentials|`HKEY_CURRENT_USER\Software\Mobatek\MobaXterm\C`|
|Passwords  |`HKEY_CURRENT_USER\Software\Mobatek\MobaXterm\P`|

__If you have NOT set a master password in MobaXterm:__

1. Credentials would look like:

   ```
   Name             Type        Data
   example.com      REG_SZ      root:bSj4VWbHezNH3tTY9Nil2RzJX57p7/S6KqMw8VsiT/WH+I8p03pqnInAu
   ```

   You can reveal credential by:

   ```console
   $ ./MobaXtermCipher.py dec -sp 165821882556840 bSj4VWbHezNH3tTY9Nil2RzJX57p7/S6KqMw8VsiT/WH+I8p03pqnInAu
   HyperSine
   ```

   where `165821882556840` is the value `SessionP` stored in `HKCU\Software\Mobatek\MobaXterm`. Please modify it based on you environment.

2. Password would look like:

   ```
   Name                         Type        Data
   ssh22:root@45.32.110.171     REG_SZ      F0+wuBvbe9qPW6ypiOeYHTHhKdShRc/nXaM1Ky1jeTfw46TzQoSesX9buGm0WW36yP4lhH70ZCHZpEo4wLJhIl1
   ```

   You can reveal password by:

   ```console
   $ ./MobaXtermCipher.py dec -sysh ShadowSurface -sysu DoubleSine -h 45.32.110.171 -u root F0+wuBvbe9qPW6ypiOeYHTHhKdShRc/nXaM1Ky1jeTfw46TzQoSesX9buGm0WW36yP4lhH70ZCHZpEo4wLJhIl1
   Lw3+cZ2s.w@U@f]U
   ```

   where `ShadowSurface` is my computer hostname and `DoubleSine` is my computer username. 
   
   If the password is stored on your computer, `-sysh` and `-sysu` can be omitted. 
   
   By the way, the example I give is a real SSH connection. But don't be happy too early, I've already delete that server.

3. All credentials and passwords can be revealed by `ShowMobaXterm.py`:

   ```console
   $ ShowMobaXterm.py 12345678
   ------------------Credentials-------------------
   [*] Name:     example.com
   [*] Username: root
   [*] Password: HyperSine

   -------------------Passwords--------------------
   [*] Name:     ssh22:root@45.32.110.171
   [*] Password: Lw3+cZ2s.w@U@f]U

   [*] Name:     root@45.32.110.171
   [*] Password: Lw3+cZ2s.w@U@f]U
   ```

__If you have set a master password in MobaXterm:__

1. Credentials would look like:

   ```
   Name             Type        Data
   example.com      REG_SZ      root:0XROpGmLAYVx
   ```

   You can reveal credential by:

   ```console
   $ ./MobaXtermCipher.py dec -p 12345678 0XROpGmLAYVx
   HyperSine
   ```

   where `12345678` is the master password you set.

2. Password would look like:

   ```
   Name                         Type        Data
   ssh22:root@45.32.110.171     REG_SZ      1du11XKQBOxud/FWh4ouWA==
   ```

   You can reveal password by:

   ```console
   $ ./MobaXtermCipher.py dec -p 12345678 1du11XKQBOxud/FWh4ouWA==
   Lw3+cZ2s.w@U@f]U
   ```

   where `12345678` is the master password you set.

3. All credentials and passwords can be revealed by `ShowMobaXterm.py`:

   ```console
   $ ShowMobaXterm.py 12345678
   ------------------Credentials-------------------
   [*] Name:     example.com
   [*] Username: root
   [*] Password: HyperSine

   -------------------Passwords--------------------
   [*] Name:     ssh22:root@45.32.110.171
   [*] Password: Lw3+cZ2s.w@U@f]U

   [*] Name:     root@45.32.110.171
   [*] Password: Lw3+cZ2s.w@U@f]U
   ```

   where `12345678` is the master password you set.

## 4. Mac 系统下的使用
   如果使用 Mac 系统，通过brew 安装的 Python  需要启用虚拟环境 进行操作。
    使用虚拟环境:

创建一个虚拟环境：
bash
复制代码
   ```
python3 -m venv ~/myenv
   ```
激活虚拟环境：
bash
复制代码
   ```
source ~/myenv/bin/activate
   ```
在虚拟环境中安装 pycryptodome：
bash
复制代码
   ```
pip3 install pycryptodome
   ```
安装完成后，你可以运行你的脚本，虚拟环境中的包会被自动使用。

<img width="1217" alt="image" src="https://github.com/user-attachments/assets/439b225a-647d-48de-b9ce-5917e1491671">

找到 MobaXterm.ini 文件中的 密码字符串

<img width="1216" alt="image" src="https://github.com/user-attachments/assets/2afc11a2-89a2-4496-9667-079e84ef1ec8">

在部署好的虚拟环境中执行解密,正常会返回解密后的明文密码
   ```
python3 /Downloads/MobaXtermCipher_fixed.py dec -sp 1 mTVD0000000000000000000000000000000000000000000000BfC70
   ```

<img width="1258" alt="image" src="https://github.com/user-attachments/assets/7fbab86a-e475-4cdf-867b-8162c601d1a5">

解密完成后，删除创建的临时虚拟环境
```
  rm -rf ~/myenv
```
```
  ls ~/myenv
```
如果返回 No such file or directory，说明删除成功。
