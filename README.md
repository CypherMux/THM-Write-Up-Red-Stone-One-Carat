# THM-Write-Up-Red-Stone-One-Carat

-------------------------------------
Hello everybody this will be a write up and walkthrough the room "Red Stone One Carat" on TryHackMe, the first room of the Red stone series. This room requires basic knowledge of the scripting language Ruby in order to fully grasp and work your way through the room so I heavily recommend to at least cover the basics of the language before we continue. I will explain each process in depth and why it works the way it works but if you do want a refresher on Ruby, which honestly I recommend to even grasp this room, here is a link to a beginner level guide:

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

Now this is where the fun begins! I spent nearly 3 1/2 hours on this room just trying to get the last flag so brace yourselves this room's creator wants the people that are trying to complete it to go crazy.

Breaking out of the Cage
------------------------













