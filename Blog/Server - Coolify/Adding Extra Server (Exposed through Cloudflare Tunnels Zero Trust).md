Is it possible to connect a server exposed through Cloudflare Tunnels / Zero Trust? I can connect with ease through the CLI - having already configured ssh to use cloudflared in ".ssh/config" - however when I try to connect within Coolify - the server cannot be reached. I am trying to use my Home Server - which I have exposed in a separate domain (Port 22 specifically) as a Swarm worker - and right now I'm failing to connect it to my main Hetzner Coolify instance. Thanks in advance!

Darren![]
    
    Coolify is running inside a container so does not know about the sshconfig or cf proxy cmd A quick workaround is to run ssh -L 222:localhost:22 sshconfighost Then in coolify use host.docker.internal with port 222 There is a couple of other ways but I need to try them first
    

- ### enb5 _—_ Today at 3:17 PM
    
    Hey, thank you for the tip, however I am not quite understanding how I would go about this. Is there a simpler solution? Or maybe if you could assist with some more instructions - I would really appreciate it. Thank you!
    

- ### Darren Today at 5:33 PM
    
    When you say you can connect with ease Connect from where?
    

- ### enb5 _—_ Today at 8:22 PM
    
    SSH to the Hetzner server - I have the .ssh/config setup like this: Host ssh.domain.com ProxyCommand cloudflared access ssh --hostname %h User e IdentityFile ~/.ssh/id_ed25519-home-server IdentitiesOnly yes --- I also made sure to remove the passphrase from the IdentityFile key just in case.
    

- I can then run "ssh ssh@domain.com" and connect with 0 struggle
    

However when I run it from Coolify - it's unreachable

![Image](https://media.discordapp.net/attachments/1290018407961333911/1290379161571164223/image.png?ex=66fc3ebe&is=66faed3e&hm=7f20710949b959e1696d2098aaf07f806895822068a5355d7c500450e7544cf7&=&format=webp&quality=lossless&width=550&height=188)

- ### Darren Today at 8:58 PM
    
    im confused, i thought it was your home that was behind a tunnel, or you mean both are behind seperate tunnels
    

- the way im reading it , youre trying to go from hetzner to your home, not the other way around
    

### enb5 _—_ Today at 9:05 PM

Sorry I may have explained it wrong. Yes - I am trying to go from Hetzner to Home Server (adding the Home Server as an external server in the Hetzner instance)

- Can you help me set that up? Thanks again for the help - really appreciate it
    

- ### Darren Today at 9:07 PM
    
    so regarding your home
    

- it is also using a tunnel?
    

- ### enb5 _—_ Today at 9:08 PM
    
    Yes - I have exposed port 22 through Cloudflare Zero Trust
    

- My Hetzner server does not use cloudflare tunnels
    

- ### Darren Today at 9:08 PM
    
    but u said above u used cf to connect to hetzner using ssh
    

- ### enb5 _—_ Today at 9:08 PM
    
    It has a public IP which I use to connect to my domains & coolify
    

- ### Darren Today at 9:09 PM
    
    so did u mean u used cf to connect to home
    
- ### enb5 _—_ Today at 9:09 PM
    
    Oh sorry mb
    
- ### enb5 _—_ Today at 9:09 PM
    
    I use Cloudflare to connect to my home server
    

- Zero Trust
    
- ### enb5 _—_ Today at 9:09 PM
    
    which required the following setup
    

- cloudflared access ssh --hostname %h
    

- in my .ssh/config
    

- basically - from my Hetzner server - after setting up the .ssh/config - I can easily connect to my home server
    
- ### enb5 _—_ Today at 9:10 PM
    
    however when I try to do the same through coolify, I get this error
    

- which makes sense - given that it probably uses it's own .ssh/config within it's docker container
    

- ### Darren Today at 9:11 PM
    
    yes
    

- because coolify is using ssh inside a container not from the vps
    

- ### enb5 _—_ Today at 9:11 PM
    
    yeah
    

- ### Darren Today at 9:11 PM
    
    so it doesnt have your ssh config or cloudflared
    

- so the quick workaround
    

- ### enb5 _—_ Today at 9:11 PM
    
    so is it not possible to use cloudflared as a proxy?
    

- ### Darren Today at 9:11 PM
    
    is in your terminal on vps
    

- ### enb5 _—_ Today at 9:11 PM
    
    okay
    

- ### Darren Today at 9:12 PM
    
    ssh -L 222:localhost:22 ssh.domain.com
    

- ### enb5 _—_ Today at 9:12 PM
    
    i'm sshd into my vps right now on hetzner so I will try this rn
    

- okay login worked
    

![Image](https://media.discordapp.net/attachments/1290018407961333911/1290390913268322445/image.png?ex=66fc49b0&is=66faf830&hm=a5a1914fec305864cf937826d679196162d815f0eb795ffec63d809e026573fb&=&format=webp&quality=lossless&width=550&height=164)

- ### Darren Today at 9:13 PM
    
    ok now in coolify just add server and select host.docker.internal and port 222
    

- ### enb5 _—_ Today at 9:13 PM
    
    i'll try that rn
    

- thanks again
    

- ### Darren Today at 9:13 PM
    
    its not a great solution, u would need to run this every restart
    

### enb5 _—_ Today at 9:13 PM

so here I will specify as the domain

![Image](https://media.discordapp.net/attachments/1290018407961333911/1290391139144433889/image.png?ex=66fc49e5&is=66faf865&hm=7db79845eaa557ceb66ed1656998a35904fe368961204e973f98c274096c8b34&=&format=webp&quality=lossless&width=339&height=350)

- the host.docker.internal
    

- ### Darren Today at 9:13 PM
    
    the other way is to sym link cf and cp ssh config into coolify container
    
- ### enb5 _—_ Today at 9:14 PM
    
    understood - i'll add it to a startup script maybe
    

- ### Darren Today at 9:14 PM
    
    yes host is that and change port to 222
    
- ### enb5 _—_ Today at 9:14 PM
    
    seems like it could break easily
    

- which user btw?
    

- root
    

- ### Darren Today at 9:14 PM
    
    the final option is to use the cf private networking
    

- yes root
    

- ### enb5 _—_ Today at 9:14 PM
    
    I am not using root network here
    

- user*
    

- at my home server
    

- ### Darren Today at 9:14 PM
    
    well whatever user you use to login
    

- with ssh
    

- ### enb5 _—_ Today at 9:14 PM
    
    to my home server?
    

- validating rn
    

![Image](https://media.discordapp.net/attachments/1290018407961333911/1290391579990818888/image.png?ex=66fc4a4e&is=66faf8ce&hm=e0234adc98433503ee5645cb9dd2a49a82a96908ab8f1f4f31835363b730630a&=&format=webp&quality=lossless&width=513&height=350)

![Image](https://media.discordapp.net/attachments/1290018407961333911/1290391637075300362/image.png?ex=66fc4a5c&is=66faf8dc&hm=cb04e42d1520ca02e853881b1f1436627e429cd2d9d518211e4fa1c7ba65edc3&=&format=webp&quality=lossless)

- the localhost server uses the same ip/domain
    

- I think that's causing it to break
    

- is the only option now to do private networking?
    

- ### Darren Today at 9:18 PM
    
    hmm you can add the same server ip
    

- oh
    

- in your terminal
    

- can u jsut quickly run
    

- ssh user@lcoalhost:222
    

- where user is what u use to log in
    

- ### enb5 _—_ Today at 9:18 PM
    
    okay 1 second

![Image](https://media.discordapp.net/attachments/1290018407961333911/1290392721198022666/image.png?ex=66fc4b5f&is=66faf9df&hm=d0380a6d599d2afa8cb17a9dce072478fdaaac262b6f369029db3bc08e817a7c&=&format=webp&quality=lossless&width=550&height=149)

- it doesn't work with either root / e (my username on the home server)
    

- ### Darren Today at 9:20 PM
    
    it needs to be ran ion your vps
    

- ### enb5 _—_ Today at 9:20 PM
    
    yes
    

- i am running it there
    

- sshd into it
    

- root@amd-server
    

- ### Darren Today at 9:20 PM
    
    is it still running on 222
    

- can u check netstat -ntlp
    

### enb5 _—_ Today at 9:21 PM

the connection is active here

![Image](https://media.discordapp.net/attachments/1290018407961333911/1290392992166838313/image.png?ex=66fc4b9f&is=66fafa1f&hm=9a3b842b9873699120ea3c5bb7bb0815ba4359b943557935d62bd2295d6303c4&=&format=webp&quality=lossless&width=550&height=304)

wait pls

discord limit

[https://notepad.pw/T9FcwUvmPGbDJbn2pioC](https://notepad.pw/T9FcwUvmPGbDJbn2pioC "https://notepad.pw/T9FcwUvmPGbDJbn2pioC")

[notepad.pw](https://notepad.pw/T9FcwUvmPGbDJbn2pioC)

Save your notes online for free and share them with friends!


![Image](https://media.discordapp.net/attachments/1290018407961333911/1290393264226304000/image.png?ex=66fc4be0&is=66fafa60&hm=8c55b6f9ac1ee881aaa364c8724e30471388ebd8a6f25551b400c16eb92f4621&=&format=webp&quality=lossless&width=550&height=262)

- ssh is present at 222
    

- wait
    

- isn't it -p {PORT}
    

- yes it worked
    

- lol
    

- ### Darren Today at 9:23 PM
    
    ah ok
    

### enb5 _—_ Today at 9:23 PM

![Image](https://media.discordapp.net/attachments/1290018407961333911/1290393616530804776/image.png?ex=66fc4c34&is=66fafab4&hm=4202cd571f3df2c060893d36177cf63ab04f08716ddb4909ef4795c9eac05faf&=&format=webp&quality=lossless&width=550&height=111)

- it's asking for the password though
    

- ### Darren Today at 9:23 PM
    
    ok yes add key
    

- ### enb5 _—_ Today at 9:23 PM
    
    which isn't good
    

- will try to specify identity
    

- ### Darren Today at 9:24 PM
    
    double check the keys match in coolify and at home
    

- ### enb5 _—_ Today at 9:24 PM
    
    worked
    


![Image](https://media.discordapp.net/attachments/1290018407961333911/1290393873268473950/image.png?ex=66fc4c71&is=66fafaf1&hm=0a6d8d9f091cd2512d844d9117a93260ec46bf8c640e8530f834116ae496fcd7&=&format=webp&quality=lossless&width=550&height=196)

- ### Darren Today at 9:24 PM
    
    ok not try again in coolify
    

- ### enb5 _—_ Today at 9:24 PM
    
    yeah trying
    

![Image](https://media.discordapp.net/attachments/1290018407961333911/1290394033600073790/image.png?ex=66fc4c97&is=66fafb17&hm=13c9f2a52f43ae899d7a7da0299b57f37217ce88e5189f4076585b572a1cc675&=&format=webp&quality=lossless&width=405&height=350)

- Server is not reachable. Please validate your configuration and connection. Check this documentation for further help. Error: ssh: connect to host host.docker.internal port 222: Connection refused
    

- using correct key also
    

- ### Darren Today at 9:26 PM
    
    im thinking its an ip issue as 222 is only listening on 127.0.0.1 not the docker ip
    

- can u get the docker ip
    

- ### enb5 _—_ Today at 9:26 PM
    
    can you remind me how to
    

- ### Darren Today at 9:26 PM
    
    then change original command to be dockerip:222:locahost:22
    

- ### enb5 _—_ Today at 9:26 PM
    
    I have forgotten how
    

- ### Darren Today at 9:26 PM
    
    ip addr will show all
    

- ### enb5 _—_ Today at 9:26 PM
    
    okay
    
- I'm guessing it's the docker0
    

- right?
    

- ### Darren Today at 9:28 PM
    
    yes
    

- ### Darren Today at 9:30 PM
    
    can u goto terminal in coolify container
    

- ### enb5 _—_ Today at 9:30 PM
    
    yeah 1 second
    

- ### Darren Today at 9:30 PM
    
    and try ssh docker.host.internal -p 222
    

- ### enb5 _—_ Today at 9:30 PM
    
    okay sure
    

- ssh docker.host.internal -p 222 ssh: Could not resolve hostname docker.host.internal: Name or service not known
    

- I ran docker exec -it coolify bash


- ### Darren Today at 9:32 PM
    
    sorry i keep saying it wrong way around
    its
    host.docker.internal

### enb5 _—_ Today at 9:32 PM

ah okay

connection refused

ssh: connect to host host.docker.internal port 222: Connection refused

22 works

### Darren Today at 9:33 PM

yeah cos 22 is listening on 0.0.0.0

- ### enb5 _—_ Today at 9:33 PM
    
    requires password though
    

### Darren Today at 9:33 PM

ok

change oprigianl to be 0.0.0.0:222:localhost:22

### enb5 _—_ Today at 9:33 PM

sorry can you guide me how to

- where should I update the mapping
    

- didn't expect this to be such a pain
    

### Darren Today at 9:34 PM

if the original termianl is still open

- ctrl c
    

### enb5 _—_ Today at 9:34 PM

thank you for your time

- ### Darren Today at 9:34 PM
    
    then exit
    

### enb5 _—_ Today at 9:34 PM

okay

- closed the connection
    

- you want me to ssh in 22
    

### Darren Today at 9:35 PM

no

- ### enb5 _—_ Today at 9:35 PM
    
    root@amd-server:~# ssh -L 222:localhost:22 ssh.enesbala.com
    

- to -L 22
    

- ..
    

- or no
    

- ### Darren Today at 9:35 PM
    
    no
    

- ### enb5 _—_ Today at 9:35 PM
    
    ah okay
    

### Darren Today at 9:35 PM

check its not running on 222

- netstat -ntlp
    

- so itrs actually 100% closed
    

- ### enb5 _—_ Today at 9:35 PM
    
    not running
    

- port does not appear
    

### Darren Today at 9:36 PM

ok now ssh -L 0.0.0.0:222:localhost:22 ssh.enesbala.com

- ### enb5 _—_ Today at 9:36 PM
    
    okay done
    

![Image](https://media.discordapp.net/attachments/1290018407961333911/1290396894031253523/image.png?ex=66fc4f41&is=66fafdc1&hm=849ae4fd6e8f6ad4a5ccf3308b81a87266cc93898e1366b22109aacfc0f6eee7&=&format=webp&quality=lossless&width=550&height=226)

- ### Darren Today at 9:36 PM
    
    ok now go back to coolify
    

- first in container
    

- ### enb5 _—_ Today at 9:36 PM
    
    okay
    

- okay
    

- ### Darren Today at 9:36 PM
    
    and then ssh host.docker.internal
    

- -p 222
    

### enb5 _—_ Today at 9:37 PM

![Image](https://media.discordapp.net/attachments/1290018407961333911/1290397071853097102/image.png?ex=66fc4f6c&is=66fafdec&hm=2d423f270c368cb1390259b3e141a29da5582b780e4f573b99d7f10ad85a688b&=&format=webp&quality=lossless&width=550&height=115)

- progress
    

- what about the password?
    

- ### Darren Today at 9:37 PM
    
    ok now do it in website
    

- ### enb5 _—_ Today at 9:37 PM
    
    should i specify the identity
    

- ### Darren Today at 9:37 PM
    
    u need to turn off password on your home tbh
    

- ### enb5 _—_ Today at 9:37 PM
    
    yeah true
    

- ### Darren Today at 9:38 PM
    
    jsut see what happens in coolify now as logn as keys are in both
    

- ### enb5 _—_ Today at 9:38 PM
    
    Server is not reachable. Please validate your configuration and connection. Check this documentation for further help. Error: root@host.docker.internal: Permission denied (publickey,password).
    

- ### Darren Today at 9:38 PM
    
    they arent in both
    

- have u added hte key to home
    

- that coolify is using
    

- ### enb5 _—_ Today at 9:38 PM
    
    yeah I did
    

- to the approved keys
    

- or whatever it's called
    

- i can check again
    

![Image](https://media.discordapp.net/attachments/1290018407961333911/1290401631413735495/image.png?ex=66fc53ab&is=66fb022b&hm=b67b1e7a72ec6cb0a6993c70d8df508f38de978c2ddccdb2495cc2e3d8fd0511&=&format=webp&quality=lossless&width=550&height=68)

- ### enb5 _—_ Today at 9:56 PM    
    adding this to the bottom of the file

- okay restarted
trying again

- same issue ffs
- trying the root thing    

- ### enb5 _—_ Today at 9:57 PM

- okay updated the authorized_keys
- on /root/.ssh

- it worked

### Darren Today at 9:59 PM

don't forget to make sure it reruns every reboot

make a startup script or sth

if you modified coolify container don't forget you will might need to redo it after an update