# THM-Write-Up-Red-Stone-One-Carat

-------------------------------------
Hello everybody this will be a write up and walkthrough of the room "Red Stone One Carat" on TryHackMe, the first room of the Red stone series. This room requires basic knowledge of the scripting language Ruby in order to fully grasp and work your way through the room so I heavily recommend to at least cover the basics of the language before we continue. I will explain each process in depth and why it works the way it works but if you do want a refresher on Ruby, which honestly I recommend to even grasp this room, here is a link to a beginner level guide:

https://www.ruby-lang.org/en/documentation/quickstart/4/

With that being said though, let's start up our lab machines and begin the walkthrough!

Initial Access
--------------

In order to gain access to the challenge to begin with, we have to find out what ports are open and which ports are not to see our attack surface. Let us start by using a ```nmap```command such as:

```nmap -sV -p- MACHINE_IP```

If you do not know what this command does then please read the following:

```nmap```:This part of the command tells the terminal to use the tool nmap, a network scanning tool. This tool is extremely useful in many scenarios including our own.

```-sV```:This flag tells nmap to probe the open ports to determine the service versions running on them. We should write down any info we collect as it may be useful later.

```-p-```:This flag tells nmap to scan ALL 65,535 ports available on a network. This is a crucial step as CTF creators often hide services on high, non-standard ports. A non-standard port is any port above 1023, the ones 1023 and below are called standard ports.

As a result of our scan, you should see the following in your terminal:

<img width="961" height="548" alt="Screenshot From 2026-10-22 15-13-24" src="https://github.com/user-attachments/assets/abb19161-7ec1-48b6-b28b-02c06ac33c44" />

As we can see from the image above, we only have one port open which is port 22 or the SSH server. Even though we did get a huge hint from the creator of this room by the sentence before our first question which is:

"Start with SSH bruteforce on user noraj"

It is still extremely important to scan all ports just in case a higher, lesser known port may be applicable to us in the progress of this room whether that be at the very end or right now. Searching all attack vectors and not just the ones handed to you on a silver platter is vital in a lot of CTFs and real world scenarios. Anyways besides that, now we know that the only port open on this machine is port 22 or the SSH server for certain and we can continue on and attempt to brute force this machine.

Brute Forcing SSH
------------------------

Now that we know for certain that SSH is the only attack vector we have and the room creator wants us to brute force it to obtain the password let's do so. I will be using "rockyou.txt" to obtain the password as it is the list most broadly used across CTFs and is most likely the correct list to use for this. Let's start by using the command:

```hydra -l noraj -P WORDLIST ssh://MACHINE_IP -t 4 -vv```

If you do not know what this hydra command does or a minor understanding please read below as this command is vital to a majority of password brute forcing:

```hydra```:This command tells the terminal to use the tool hydra, a password brute forcing tool.

```-l noraj```:This flag is a shortened form of the word "login" and represents the username in which you will use, for instance our username we will use is noraj as it is given to us by the room creator.

```-P WORDLIST```:This flag tells hydra to pull the passwords to be used for the brute forcing from this wordlist, for example in our case we will be using "rockyou.txt". If you do not have it here is a link to the .txt file:

https://github.com/RykerWilder/rockyou.txt

and also the defacto wordlist for everything you may need to brute force,SecLists, I recommend this one much more as it includes rockyou.txt and many more wordlists applicable to a variety of scenarios:

https://github.com/danielmiessler/SecLists

```ssh://MACHINE_IP```:This command string tells hydra what type of service it is targeting and the target IP of said service. For example our service would be SSH and the target IP is the target machine's IP, which again is on the top right of your browser for this room.

```-t 4```:This flag will tell hydra to run 4 tasks in parallel with each other to speed the process up. I recommend only doing a max of 4 tasks at a time though as many servers are rate limited and you will come across errors if too many tasks are running in parallel.

```-vv```:This flag is completely optional if you want to put it or not but all that it does is it makes the output of the command extremely verbose, which means it will tell you everything it is doing as it brute forces the login form. I only put this so I can see that what I input is actually responding on the other side of the terminal lol.

Now that we ran the command let's see how things are going for our terminal in terms of solving our missing password dilemma:

<img width="961" height="607" alt="Screenshot From 2026-10-22 15-35-23" src="https://github.com/user-attachments/assets/773d7622-f2e7-492c-8fbc-a3e971988366" />

10 HOURS!! Yeah absolutely not we won't be waiting that long. Even though we did do the command right this list is simply too long to be properly utilized for such a task. Let's see if the room creator left any hints for us that might help us in quickening this brute forcing process. Go to the first question and click the little light bulb for a hint, this will give you a hint in the bottom left of your screen which should look something like this:

<img width="961" height="237" alt="Screenshot From 2026-10-22 15-41-21" src="https://github.com/user-attachments/assets/fb460da9-5f4e-4021-8a20-dbc28e8c243e" />

This hint is probably the most helpful thing we have received so far. All we have to do is limit the list down to words which contain "bu" and we are golden! We can do this using the ```grep``` command and then piping the output into a text file of our choosing. We can do this by using this command:

```grep "bu" WORDLIST > newlist.txt```

This is a step by step of what this command does:

```grep "bu"```:This tells the terminal to use the command grep which is a terminal searching tool, the "bu" portion tells grep to only search for words that contain "bu", like burger for example.

```WORDLIST```:This tells grep to search through this wordlist in particular and pick out only the words that contain "bu", for example the wordlist I am using is the SecList's rockyou.txt file which would be "SecLists/Passwords/Leaked-Databases/rockyou-75.txt".

```>```:This simply tells the output of the first command to transfer over to our text file.

```newlist.txt```:This text file can be any name you want but it will be where our new filtered list will be so make sure to name it something you'll remember!

Now that we have this new wordlist, do the hydra command again exactly the same except this time use your new filtered down wordlist instead of the absurdly long rockyou.txt one. After hydra is done brute forcing the password, your terminal should look something like this:

<img width="958" height="822" alt="Screenshot From 2026-10-22 15-55-12-obfuscated" src="https://github.com/user-attachments/assets/190121d2-6fe9-4857-9227-17ead46fc33e" />

Due to TryHackMe's policy I am unable to show the answer but besides that we have found the password for the username we brute forced, now all we need to do is access the SSH server!

Gaining a foothold
-------------------
Now that we have officially obtained the password to access this users SSH server lets utilize that! To access the SSH server input the following command:

```ssh noraj@MACHINE_IP```

This should be pretty self explanatory on what the command does so I shouldn't have to explain it to much in depth. After executing that command inputting the password as asked, you should be looking at something like this on your screen:

<img width="958" height="515" alt="Screenshot From 2026-10-22 16-03-11" src="https://github.com/user-attachments/assets/bfda9c17-b245-47ce-b675-dc37eb55d0cc" />

Now this is where the fun begins! I spent nearly 3 1/2 hours just trying to get the final answer in this room so I'll attempt to explain everything in depth so no confusion arises along the way like I had myself. With that being said, let's begin!

The Restricted Shell Trap
--------------------------

Now that we are in the shell, a new problem arises, this does not look like any normal shell. If we were to start with an ```ls``` command the server tells us that the command is not found. In fact, a ton of useful commands are not found as shown by the image below:

<img width="958" height="740" alt="Screenshot From 2026-10-23 15-01-36" src="https://github.com/user-attachments/assets/3fb3e206-3d0b-4854-9cde-eb4ebabc65d4" />

Although I did manage to see where I am in the directory via the ```pwd``` command the rest of the commands are dysfunctional as we are in a restricted shell. This means we will have to use workaround commands in order to traverse around this shell as it is VERY locked down. I did find one command that worked which is the ```echo``` command as shown in the image below:

<img width="958" height="232" alt="Screenshot From 2026-10-23 15-05-38" src="https://github.com/user-attachments/assets/33f96455-68b5-4a9a-bbb3-11b05dd3616f" />

Now we can use echo to our advantage 100%. Since ```ls``` is blocked, we will need to find another way to list the contents of the directories that are in this system. We can do this by abusing a certain way the shell functions. Put this command in as follows:

```echo *```

Because of a quirk with how the shell functions it will attempt to expand the wildcard, which is the * in our scenario, before it reads the block list put in place. This allows us to abuse this to display all of the files in the current directory we are in as shown below:

<img width="958" height="108" alt="Screenshot From 2026-10-23 15-09-38" src="https://github.com/user-attachments/assets/183223b6-4112-441b-a4d4-5e2c77ff9073" />

There is our first flag the "user.txt" file! Let's try to read it using the command:

```cat user.txt```

<img width="958" height="108" alt="Screenshot From 2026-10-23 15-12-34" src="https://github.com/user-attachments/assets/7f745d84-040e-4fe3-bccb-c3aaa445bd6c" />

Of course ```cat```is blocked as well why wouldn't it be. But no worry, we have another way to read the text file without the use of ```cat```.Again, we can abuse a certain quirk with how the shell functions in conjunction with the ```echo```command to get our flag. Input the following command:

```echo $(<user.txt)```

This command works in three stages that go as follows:

```echo```:This is just the simple echo command, which outputs whatever you tell it to say in the terminal, such as "hello world" in my first example of using it in this room.

```$()```:This is what is called a command substitution, It will take whatever is inside the parentheses and evaluate it as part of the full command. This is essential as it essentially connects the echo command and our "user.txt" file.

```<user.txt```: This is an input redirection command. It essentially tells the shell to put the text into its memory to be used later in the command. This isn't using the ```cat```command, so the block list does not notice a thing. 

After it inputting this command and letting it execute on the server, your terminal should show the following:

<img width="958" height="108" alt="OBFSCUSER TXT" src="https://github.com/user-attachments/assets/081c1382-2256-46da-89cc-3cb0bbace6ea" />

We got our first official flag! Due to TryHackMe's policy again I am unable to show the answer but still we did it! With that being said, let us proceed to the final flag of this room!

Obtaining the root.txt flag
----------------------------

Now this is where things get a little complicated so make sure to follow along carefully. As you may have noticed in the image showing the user.txt file in the directory we had another file alongside it called "bin". Now we do not know yet if it is a file or a directory yet but still we know it is another thing in there which means we should explore it further to see if it will be importance to us in the search. Since ```ls```is on the block list we should also find a way to see hidden files/directories inside /home/noraj. Let's first start with discovering the hidden files/directories in here. We can use this by using a similar command to the first ```echo```command that listed the visible files/directories by inputting the command:

```echo .*```

In Linux, any file that starts with a "." is considered a hidden file, these are often called dotfiles. Using the standard ```echo *``` command will not show them to keep your terminal clean. When you type ".*" instead of just * this is essentially telling the shell to find every file in the directory that starts with a dot, which is good for us as these are the hidden files. The shell then translates it to show this in our terminal:

<img width="958" height="108" alt="Screenshot From 2026-10-23 15-39-17" src="https://github.com/user-attachments/assets/ed6a2e66-5994-43bf-911d-4de0d29ac94f" />

What is .hint.txt? That file seems insanely important for our search of finding this root.txt file. Let's use the same command we used previously to read the user.txt file to read this one by inputting ```echo $(<.hint.txt)```.

<img width="958" height="108" alt="Screenshot From 2026-10-23 15-42-31" src="https://github.com/user-attachments/assets/313e2fcc-bcab-4a4f-9442-351acaa1d114" />

Maybe take a look at local services? Let's save this info for later, if the creator of this room left that in a file literally called hint then it must be important. With that being said though let's now see what is in that bin file. Let's first try to read its contents using the same command we used for the hint file and the user file but this time for bin. After we input ```echo $(<bin)``` we get the following:

<img width="958" height="108" alt="Screenshot From 2026-10-23 16-04-33" src="https://github.com/user-attachments/assets/1a3d20b8-5e97-4640-9bfc-0f06b8ddaed0" />

Although it appears empty, that does not mean nothing is in it. Let's try to get into it using another wildcard expansion quirk, input the following command into your terminal:

```echo bin/*```

If the file is a folder rather than a file, this will show the contents of the folder. After the command is executed you should see the following in your terminal:

<img width="958" height="108" alt="Screenshot From 2026-10-23 16-06-58" src="https://github.com/user-attachments/assets/9ad882dd-6336-442e-b3b1-6a29315e5059" />

Now we're getting somewhere! We have two files, rzsh and test.rb. What we are most interested in at the moment is that test.rb file that's a ruby script! If we can see what it does we might be able to abuse it to get root privileges and obtain our flag! Since we are in a Z shell, as shown by zsh: before certain commands and the rzsh file in our bin folder, we can use its built in commands to read the test.rb file. This command is pretty complex so I will explain it after but it goes as follows:

```zmodload zsh/mapfile```

```print ${mapfile[bin/test.rb]}```

Input the first command and then input the second command on the next line, this is what the command does in depth:

```zmodload```:This is a built in zsh command that is literally hardcoded into the shell, as it is not an external binary we can use it to our advantage. This tells the shell to load an external module or a shared library into the current session, such as the one we are in.

```zsh/mapfile```:This is the specific module we are loading with the ```zmodload``` functionality. For instance the module we are inputting here creates a special array in the memory of the shell called $mapfile. When the module activates, you don't need to open a file with an external program, the shell in it of itself is the one to open it for you and maps the contents of the file into memory instantly. This is why the module is called mapfile to begin with it "maps" the "file".

```print```:This is just another zsh command that is built into the shell. It is extremely similar to echo as it outputs text to your terminal. Because this is built in and ```cat``` is not, we can use this to read the text in our test.rb file.

```${}```:This is what is called a parameter expansion syntax. It essentially tells the shell to evaluate whatever is inside the braces and to give you the result.

```mapfile[bin/test.rb]```:This is our specific array lookup. We are telling the shell to go into the $mapfile array we had just loaded previously, find the index of bin/test.rb, and then grab its value. In our case that would be the text inside.

After this inputting this chain of commands and executing them, you should see the following in your terminal:

<img width="958" height="668" alt="Screenshot From 2026-10-23 16-22-03" src="https://github.com/user-attachments/assets/27132e0d-bf34-4081-bca2-e72013604e5b" />

Ignoring the fact that I messed up the command the first time(no backspacing to delete anything in this shell lol) we can now read the contents of our test.rb file! Let's analyze this ruby script piece by piece to understand exactly what it does:

```require 'rails'```: This code will load the "Ruby on Rails" framework. It's just included to make sure the environment has the correct and necessary libraries loaded, it doesn't pertain to our search at all it's essentially just a check for the file.

```if ARGV.size == 3```: ARGV is an array that holds the arguments passed to the scripts from the command line. In simpler words, this code tells the command line that if the user passes exactly three arguments while running this script execute the code inside the ```if``` block.

```klass = ARGV[0].constantize```:This is the first half of the vulnerability we can abuse in this script.```ARGV[0]``` will be the first argument you type.```.constantize```is a Rails method that will take a string and convert it into an actual Ruby class object. This is extremely dangerous but for us essential to get what we want privilege escalation.

```obj = klass.send(ARGV[1].to_sym,ARGV[2]```:This is the second portion of the vulnerability in this script.```ARGV[1]``` is the second argument and ```.to_sym``` will convert it into a Ruby symbol.```.send()``` is a Ruby method that will force the class provided to run a specific method of our choosing. ```ARGV[2]``` is the third argument for our input.

```else puts File.read(__FILE__) end```: This will tell the script that if you ran it with zero, one, or two,three, or even four arguments to run the ```else``` block. ```__FILE__``` is another Ruby variable that essentially just holds the path to the script we are currently looking at. So in the turn the code ```File.read(__FILE__)``` reads the script itself and prints it on the screen, which is why we are able to see it to begin with without being blocked.

Now let's get to how we can exploit this. 

The Exploit
-----------

Because of the ```.constantize``` and ```.send``` line of code we saw prior in the script we viewed, the creator of this room gave us the power to execute ANY method on ANY Ruby class, this is insanely powerful. Let's test this by running the command:

```test.rb Kernel eval "puts 1"```

This command does the following for us:

```test.rb```: This is the Ruby script we discovered prior. This will find the script, execute it for us, and then pass all arguments we give it to the said script.

```Kernel```:This is a core Ruby module, Inputting this will tell the script to take the word "Kernel" and run ```.constantize``` on it as this is considered ```ARGV[0]``` to the script. Ruby's Kernel module handles basic system level functions. This is why it is such a powerful target for us to manipulate.

```eval```:This is a built in Ruby method. When we use it the script will take the word "eval" and run ```.send(:eval, ...)``` on the ```Kernel``` class we just input prior in the same command.```eval``` is an extremely powerful Ruby method which will take a string you give it and execute it as actual Ruby code. This is considered ```ARGV[1]``` to the script.

```puts 1```: This is the payload. The script will pass the string to the ```eval``` method where Ruby will then evaluate said string and see the ```puts 1``` command. This will tell our terminal to print a "1" into the terminal. This considered ```ARGV[2]``` to the script.

Although a simple command, it will tell us if the exploit is truly there or not which is essential if we want to proceed any further with it. After inputting the above command, the following should be in your terminal:

<img width="958" height="116" alt="Screenshot From 2026-10-23 16-53-59" src="https://github.com/user-attachments/assets/b5e14da9-ea9d-4c09-8430-ed1da7175534" />

It worked! That means we can now abuse this functionality to gain access to things in this server we really shouldn't. Let's look back to that .hint.txt file we found earlier:

"Maybe take a look at local services."

On Linux systems, local services usually will communicate over the network ports which are bound to 127.0.0.1, which could also be called the localhost. Let's use that ruby script we found and use Ruby's built in networking and file tools to read the system's network connections! Let's start by attempting to read the /proc/net/tcp file. This file lists all active TCP connections on the target machine. Again, lets use the ```eval``` method we did earlier in our test by inputting the following:

```test.rb Kernel eval "puts File.read('/proc/net/tcp')"```

This is essentially the same command we did prior in our test command but instead of putting "1" in the puts command we will put the ```file.read('/proc/net/tcp')"``` command. This tells the script to read all files that reside in the directory /proc/net/tcp. After inputting the command and letting it execute, you should see the following:

<img width="958" height="1015" alt="Screenshot From 2026-10-23 18-02-01" src="https://github.com/user-attachments/assets/989e2de1-c4be-4731-8711-cce99bf5c043" />

This may look like a giant wall of gibberish but if you know what you are looking for this is extremely useful. Looking at the image, we see a ton of hexadecimal data. This data represents every active and listening socket on the system. Although Linux stores these ports in a specific format we will have to decipher and we need to manually decode. The bottom two ports represented with a "2" in this list are our active connections, which are not useful to us as we are looking for a port we are not currently connected to. Our active connections can also be signified by the "01" in the middle of the hexadecimal data. 

The main ports we are in most interest of is the ports which have the hexadecimal data reading as "0100007F". This translates from hexadecimal to 127.0.0.1, or our localhost. The "0A" means the port is currently open. As we can see in the list we have currently, there is a ton of open ports ready to receive our connection! Let's try each one and see which ones work by attempting to connect to them via netcat!

<img width="958" height="183" alt="Screenshot From 2026-10-23 18-15-42" src="https://github.com/user-attachments/assets/cc4114e2-1ffc-4b7b-b2a7-790269e6f1d9" />

Of course netcat is blocked why wouldn't it be. That's no worry though we still have a way to get netcat working in our terminal. We need to resort back to using the vulnerable Ruby script we talked about prior first to spawn a new shell that we can exploit. We can do this by inputting the following command:

```test.rb Kernel 'system' "/bin/zsh"```

This command will spawn us a new shell via the /bin/zsh directory. We need this new shell because of the way our current shell is restricted, as we are unable to do commands we need to execute such as ```nc``` in the restricted shell, meaning we must spawn a new shell. This will make a separate process and launch a new shell for us. Of course the zsh still takes over the new shell as it is what the shell is built from but the rzsh, or restricted Z shell's, restrictions are alleviated from our new shell. Once we are in this shell we are no longer restricted by the rzsh's parser, meaning we can do the next command to finally acquire netcat in this shell. Remember prior to spawning this new shell we were unable to do such a command as listed below:

```export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin```

When you type a command like ```nc``` into your terminal, the shell doesn't automatically know where that program resides on your system. Instead, it will rely on the environmental variable we know as $PATH. The $PATH variable is essentially a list of directories separated by colons. The restricted shell we had prior had overwritten this essential system to only point to the directory /home/noraj. Our solution to this problem in our new shell is to overwrite this $PATH variable with a new $PATH variable. Because /usr/bin and /bin contain all the standard Linux binaries, which include ```cat```,```ls```,```bash```, and especially so our ```nc``` command, the shell can all of a sudden read them and execute the said commands! Now that we corrected the system to finally be able to read the standard Linux binaries, let's attempt to run netcat again! 

<img width="958" height="142" alt="Screenshot From 2026-10-23 18-44-52" src="https://github.com/user-attachments/assets/65075730-acda-41ec-805e-2aa09ccc0fd2" />

The command worked and we are in a new shell, although this one is a bit odd. When I input the ```whoami``` command as listed in the image above, the terminal tells us:

"undefined local variable or method 'whoami' for main:Object"

This text is extremely important into understanding where we are, as this comment only displays when you are in a Ruby REPL shell, or in other words a Ruby Read-Eval-Print Loop shell. A REPL is an interactive console which executes code line by line. This is huge! If we input the correct command, we can read the root flag! We need to remember as well that since we are in a Ruby shell we can only use Ruby syntax for our commands. With that being said, input the following command:

```%x{cp /bin/bash /tmp/bash; chmod +s /tmp/bash}```

Let's do a deep dive on what each part of this command so we can fully grasp it:

```%x{}```: This is what is called a "parser bypass" for our situation. In Ruby, "%x" is a notation that tells the interpreter to execute the contents inside the brackets as a shell command. It's the Ruby equivalent of using back ticks for a command. (`)

```cp /bin/bash /tmp/bash```:This part of the command will use the ```cp``` command to copy the standard Bash shell binaries into the /tmp/bash directory, and names the new file the binaries reside in "bash". Because we are executing this inside the Ruby REPL, the newly coped /tmp/bash file will now be owned by the root user meaning we can use regular linux commands as root now!

```;```:This just tells the shell to finish up the command on the left and then proceed with the right command immediately, simple enough.

```chmod +s /tmp/bash```:This will change the file permissions of the file we just created /tmp/bash. The ```+s``` section of the command will set the SUID bit on the file we just created. This is the most important part of the command as it tells the kernel whenever somebody executes this file, run it with the root user's privileges and not the actual user's permissions that had ran it.

Now that we ran that command all our work in the Ruby REPL is done and we can close the shell and go back to our restricted shell. After you are back in the restricted shell you can now spawn the root shell using the directory you just created in /tmp! We can do this by executing the command:

```/tmp/bash -p```

All this command does is first, executes the file we just created in /tmp, that being "bash", and then when the system attempts to bring us back down to our standard permissions for security permissions, the ```-p``` flag forces it to mantain the root permissions we acquired. After inputting this command you should see the following in your terminal:

<img width="958" height="175" alt="Screenshot From 2026-10-23 19-08-26" src="https://github.com/user-attachments/assets/7e5fd033-a545-4463-a454-dc43a6b77cf6" />

We have officially became the root user and accomplished privilege escalation like we had been talking about prior! Now the only thing we have left to do it read the root.txt file and acquire the flag. We already know the file resides within the root directory so we can retrieve it by executing the following command in our terminal:

```cat /root/root.txt```

After reading the file with cat, you should see the following in your terminal:

<img width="958" height="115" alt="FINALFLAGOBFSC" src="https://github.com/user-attachments/assets/77ca6a0f-e024-4e5e-b035-0111a7f53c8d" />

We found the last flag and we completed the room! Thank you for following along with me and great job! Have a great day!

<img width="384" height="480" alt="giphy" src="https://github.com/user-attachments/assets/29e56fdb-6585-4a5e-a2ac-d666a4d8c7c1" />






































