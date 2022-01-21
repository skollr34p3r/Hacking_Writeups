- Basic nmap scan:
![63c4f336c1ecbbd9785ddb1684a711bf.png](file:///C:/Users/joseph.clay/.config/joplin-desktop/resources/24495500cfa3414a9fd92caadb647939.png)
Notice port 80 is open so browse to IP
![21b2a9c7ddb7bb1401d8948acab101bf.png](:/f8cacd3701ab4806ae204aebbab8368c)
It's IIS, so I wonder if the version is vulnerable

- Ran nmap to check that port specifically:
![10b60096cd69eaac5a0ae062e487bc0c.png](:/dd6c2707da8540599dca16c975a35a3b)

- Ran more detailed nmap scan:
![695b0363eae8c364972b5d84834f2cbd.png](:/7c585557e1b7402e8f98b9a1fcaad303)
Also looks like nmap thinks it may be windows xp/7
![7d4d0a088fbaf49c6b627639427692a5.png](:/db5fd8ad756f433a910dc0a23318496a)

- Noticed from nmap scan that anon ftp login is allowed:
![0c757e12803546d1138d7081f800ad23.png](:/7d2a3d6b8b04404d8fce41f8993d81f2)

- Logged into FTP using anonymous/anonymous:
![9f1686c56e7d3805655ff5d51d57bfe5.png](:/ada28f2a745f422fac9f3b653598ecbe)

- Found and downloaded file:
![0eec0d6ce2ea4ee46e70f23dba82b59c.png](:/1ea716235f8e49d78bed592fe3bbabc2)
![ad0292f1d9a6132c9d4c78d1b9abfd2e.png](:/03deffa20f1e48dd8d4007aa755b824c)

- Opened the file:
![c7f6a510b086129aa18187a1423e11c5.png](:/93d763a2d2c94788bfccb10bf98ed798)

No password as of yet, so shifting gears. Went back to detailed nmap and noticed that IIS is also running on port 9998:
![1a0f394b6dc7e45d4a588b78ce4d5a24.png](:/99119b66b7014fd6a5e1254a243d8677)

- Browsed to this location and found a login:
![f426f53585768cd1a346df2fc3922746.png](:/cb0d96e1c6a248ac8b03a2d6fba734fb)
Tried admin/admin credentials (default) and they did not work

- Searched for SmarterMail exploits:
![406b7cd4e9b905ddc5c0dfde789e2a4b.png](:/ac83e77a4ed54251b516374c664eb58c)
Not sure of build number, but see rce for build 6985. Time to enumerate.
Checked source code on site:
![08365cfd79dad78e48a25fafcc061978.png](:/d2ed3f2456d048f6bbaa63163ff11064)
Looks like the exploit is for a version after the one on the server, but it may still be vulnerable

- Download exploit
`searchsploit -m windows/remote/49216.py`

- Looking at exploit
![0da9aa9dfa0701b8e531e9120acdb8dd.png](:/dd2920c32141412c9c0bfdbf33801c34)
Seems to be trying to use port 17001 by default (I already changed the HOST value to be the target and the LHOST value to be my attack machine Tunnel IP)
![db0e5dcc2877f5f66d1e5ce2bb4badad.png](:/dacccb6241c945c0bba5d32b9d48f7a8)
Let's see if port 17001 is open on the target:
![d758cd21193d2d2f57454b9b456db434.png](:/7840c0009b5140cda8326f4fe9eb5b3c)
Oh it is! Let's attempt the exploit 

- Set up netcat listener on port 4444
![e0a97422b4186969d643af6fd6137017.png](:/d5719e579d9342b3aec79564248438b1)
- Run the exploit
![58bd150ac4f52d845314638aa7fc6b4a.png](:/5114a6ed80f142b49d87b9260c2c817f)
Didn't work
![0169e2d76eb476d3841211bdc35668c0.png](:/82261e7cdd874cf78a8de05322699501)

- Experimented with different LPORT values:
-tried 4444, 1234, 123, 443, and none worked. 

- Finally got the exploit to work with port 445:
![789556b3d4f6e86e54a3042d4c9051b8.png](:/1de136a9b7cf46ac87ab9ccf5e7555fc)

- Not seeing a users.txt, so going straight for proof.txt:
![33c4c0eef553f813aff66787e38ecedd.png](:/8c7feef1b7724b5ca2e16f6812dc732e)

