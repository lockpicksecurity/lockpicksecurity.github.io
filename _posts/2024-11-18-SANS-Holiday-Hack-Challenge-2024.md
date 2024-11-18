---
published: true
---
In this post I'll walk you through successful completion of all challenges from SANS Holiday Hack Challenge 2024 event (Prologue and ACT I for now). If you haven't signed up do so [here](https://www.sans.org/mlp/holiday-hack-challenge-2024/). It's real fun!

# Prologue
## ELF Connect (Easy)


In this challenge, I actually went through without looking at the code but this is a hack challenge so here’s what the code is telling us is the right combinations.
<p><img src="{{site.baseurl}}/images/sans2024/image001.png"></p>
Easy-peasy


## ELF Connect (Hard)
The hints suggest that we need to beat an impossible High score. 
Looking at the code at initialization of variables we find that the variable score contains our score and as such is subject to manipulation to beat that highScore variable.

<p><img src="{{site.baseurl}}/images/sans2024/image003.png"></p>

On line 247 the score variable is updated by 100 once a successful combination submission is made so we set a breakpoint in Dev tool’s debugger on that line.  

<p><img src="{{site.baseurl}}/images/sans2024/image006.png"></p>

We then make one successful combination and execution is paused at the breakpoint.
Inside the console we add 50000 to it to beat the high score and allow execution.

<p><img src="{{site.baseurl}}/images/sans2024/image007.png"></p>

Success!

<p><img src="{{site.baseurl}}/images/sans2024/image010.png"></p>


## ELF Minder 9000 (A Real Pickle)

I can’t be bothered to show you how to pass all the previous levels as it’s a real fun playing it without cheating. In this level however, you must cheat as there’s no logical path that you can take to lead the elf to the finish line.
Jumping straight into the javascript code I can find a nice reference left by the author suggesting a variable holding all level data game.entities and a reference to an editor which is not visible to us by default.

<p><img src="{{site.baseurl}}/images/sans2024/image011.png"></p>

Quite frankly enabling the editor didn’t work all too well for me as I was only able to clear the level’s obstacles. Without digging into how to enable the isEditor, I just ran the “adminControls.classList.remove('hidden')” command in the console as this is what it would have executed anyway.

<p><img src="{{site.baseurl}}/images/sans2024/image014.png"></p>

The game also holds another variable “game.segments” which contains the path that the elf would take. This is rather important as this is what’s sent to the server for validation.

<p><img src="{{site.baseurl}}/images/sans2024/image016.png"></p>
 
Initially I thought that clearing all obstacles objects by popping all entries from the game.entities array using “game.entities.pop()” and drawing the path of the elf to the finish line would work, but the server didn’t like my cheat.
Then I decided to preserve all obstacles in a separate array, remove all obstacles, draw the path to finish line and restore all obstacles from that backup array. Strangely the elf was allowed to walk over the rocks and get to the finish line, but the server didn’t accept the path. Walking on rocks wasn’t a good idea. After a number of trial and errors, the one solution that worked for me was to preserve all obstacles, temporarily remove them from the level’s map, draw a tunnel and a path at the bottom right block where the finish line is and connect to it via another tunnel, then restore the obstacles. This seems to have been accepted by the server this time. Success!  Below is my winning combination.

<p><img src="{{site.baseurl}}/images/sans2024/image018.png"></p>


# Act I
## cURLing (Easy)
This is probably the easiest of all challenges as it’s exercising the use of various parameters passed along the curl command. It’s made so easy as running “hint” on the terminal reveals the answer to you. It’ll make you type in the following in order to succeed:
{% highlight c %}
curl http://curlingfun:8080/
curl -k https://curlingfun:9090/
curl --insecure --data "skip=alabaster" https://curlingfun:9090/
curl --insecure --cookie "end=3" https://curlingfun:9090/
curl --insecure --verbose https://curlingfun:9090/
curl --insecure --header "Stone: Granite" https://curlingfun:9090/
curl -k --path-as-is https://curlingfun:9090/../../etc/hacks
{% endhighlight %}

Easy-peasy lemon squeezy.

## cURLing (Hard)
I can’t say this is much harder than the easy version of the challenge. 

Once in the terminal all you need is to list the contents of the file HARD-MODE.txt inside the current folder

<p><img src="{{site.baseurl}}/images/sans2024/image020.png"></p>

You’ll have to build one query based on most of the easy mode’s parameters and ask for a little bit more, but really nothing that you can’t find in the man page of curl.

{% highlight c %}
alabaster@curlingfun:~$ curl --cookie "end=10" --insecure --header "Hack: 12ft" --data "skip=bow" https://curlingfun:9090/
Excellent!  Now, use curl to access this URL: https://curlingfun:9090/../../etc/button

alabaster@curlingfun:~$ curl -k --path-as-is https://curlingfun:9090/../../etc/button
Great!  Finally, use curl to access the page that this URL redirects to: https://curlingfun:9090/GoodSportsmanship

alabaster@curlingfun:~$ curl -k -L https://curlingfun:9090/GoodSportsmanship
Excellent work, you have solved hard mode!  You may close this terminal once HHC grants your achievement. 
{% endhighlight %}

Success!

## Hardware hacking part 1 (Easy)

The manual already suggests that we use 3V instead of the default 5V so we flip the switch on the UART bridge to 3V. 

<p><img src="{{site.baseurl}}/images/sans2024/image022.png"></p>
 
When we connect the wires we have to make sure that GND goes to GND (almost always the black cable although not a requirement), VCC goes to VCC (red cable) and Transmit Tx and Receive Rx are always inverted to account for the connection on the other side’s receive and transmit (i.e. Tx (left) -> Rx (right);  Rx (left) – Tx (right)). We connect the USB cable to the UART bridge and press the Power button (P).  The logic of checking for proper wiring and settings is in the code 

<p><img src="{{site.baseurl}}/images/sans2024/image024.png"></p>

We can also check in the code that we’ll receive the message "success! loading bootloader...\nGo speak with Jewel Loggins for the next step!" if everything is all wired up correctly. This is done inside the function “checkit” which receives all settings and wiring and sends to the server for validation. We’ll inspect that function later in the Hard version of the challenge.

<p><img src="{{site.baseurl}}/images/sans2024/image026.png"></p>

The Hints section for this challenge we have a reference to a python script which will help us reconstruct pieces of shredded paper (small images) into original paper with the settings.

<p><img src="{{site.baseurl}}/images/sans2024/image028.png"></p>

In the items section there’ s the link to the archive containing these small images

<p><img src="{{site.baseurl}}/images/sans2024/image030.png"></p>

Once downloaded the python script will need to be executed in the same directory where the folder of extracted images is. Just remember to install all python dependencies. Once successful it generated the following image for me (a bit odd but not sure you’ll get the same results). I had to invert it horizontally to make it a bit clearer.

<p><img src="{{site.baseurl}}/images/sans2024/image032.png"></p>
 
It says that the settings are as follows:
{% highlight c %}
Baud: 115200
Parity: even
Data: 7 bits
Stop bits: 1 bit
Flow Control: RTS
{% endhighlight %}

So we set it as on the image below and hit the (S) button.  Success!

<p><img src="{{site.baseurl}}/images/sans2024/image034.png"></p>

## Hardware hacking part 1 (Hard)
Everything should be set as in the easy version of the challenge, but this time we pay more attention to the “checkit” function. We open up Firefox’s devtools (or Chrome) and navigate to the main.js file’s declaration of the checkit function on line 864.  We notice a comment about a version 1 of the API and that it should have been removed by now. Perhaps this is where we need to send our data to win the Gold for this challenge.  

<p><img src="{{site.baseurl}}/images/sans2024/image036.png"></p>

The way I’ve done it is by setting a breakpoint on line 876. Weneed to set everything correctly as described earlier and once we hit the (S) button the debugger will pause execution on that line. Step into one line to initialize the variable and then you can see all of its parameters in the console.

<p><img src="{{site.baseurl}}/images/sans2024/image038.png"></p>

This is where we made the change to the URL to account for that V1 of the API. To do so change the url.pathname to “/api/v1/complete” in the console
 
<p><img src="{{site.baseurl}}/images/sans2024/image039.png"></p> 

Resume the execution of the javascript in the debugger. Success!


## Hardware hacking part 2 (easy)
Once we enter the terminal we do “ls -alh” to display all contents of the home’s folder. This suggest we have an interesting file called access_cards, but the focus first is on the bash history file. It reveals the passcode left in there hidden in plain sight.

<p><img src="{{site.baseurl}}/images/sans2024/image041.png"></p>
 

We modify it to our needs to update record 42.

<p><img src="{{site.baseurl}}/images/sans2024/image043.png"></p>


## Hardware hacking part 2 (hard)
The elf suggest there’s another harder way Type "file access_cards" reveals it's an sqlite database file.
sqlite3 executable is available in the terminal so we run it.
In sqlite’s console we type “.tables”
It shows us two tables - access_cards and config. We type “.schema access_cards” and “.schema config” to see each table’s layout.

<p><img src="{{site.baseurl}}/images/sans2024/image046.png"></p>
 
We’ll need this later when updating the entries.
To see the contents of each table we issue the command: "select * from config" and "select * from access_cards". 
 
<p><img src="{{site.baseurl}}/images/sans2024/image047.png"></p> 
 
The hmac_secret reveals the key used in the HMAC operation. The hints were already suggesting a reference to HMAC generator functionality in cyberchef which had the key set to UTF-8 and the hashing algorithm set to as SHA256. Thanks for the hint as this could really have been any of the other 256 bit hashing algorithms. 
The “hmac_message_format” reveals that the format of the message is the access type (0 or 1) followed by the UUID.

Regardless of trying to verify the theory by replicating the HMAC signature for entry 42 it never generated the same signature as the one in the table which led me to believe I was doing something wrong and explored countless other options of trying to resolve this...
However, after all these attempts I finally got it by entering manually the generated signature:

In CyberChef put "9ed1515819dec61fd361d5fdabb57f41ecce1a5fe1fe263b98c0d6943b9b232e" as the key, enter "1c06018b6-5e80-4395-ab71-ae5124560189" as input (essentially {1}{c06018b6-5e80-4395-ab71-ae5124560189} the access set to 1 followed by the UUID). This outputs "135a32d5026c5628b1753e6c67015c0f04e26051ef7391c2552de2816b1b7096" as signature.

<p><img src="{{site.baseurl}}/images/sans2024/image050.png"></p>

Then we update the signature with the one generated by CyberChef.
{% highlight c %}
sqlite> update access_cards
   ...> set sig = '135a32d5026c5628b1753e6c67015c0f04e26051ef7391c2552de2816b1b7096'
   ...> where id = 42;
{% endhighlight %}

<p><img src="{{site.baseurl}}/images/sans2024/image051.png"></p>

## Frosty Keypad (Easy)
The elf’s already suggested there’s a book that the other elves were using to get the code. The book is easily discoverable on the map just on the upper right corner if I remember correctly. Once found you can read it at https://frost-y-book.com/.
The elves/hints also suggesting using the ottendorf /book cypher to decode the code. The note left reveals it 

<p><img src="{{site.baseurl}}/images/sans2024/image054.png"></p>
 
This is cross-referenced with the 9 digits keypad with letters. 
{% highlight c %}
2:6:1    	Letter: S	7
4:19:3 	Letter: A	2
6:1:1		Letter: N	6
3:10:4		Letter: T	8
14:8:3		Letter: A	2
{% endhighlight %}
So… SANTA reveals us the code 72682. We enter it on the keypad and success! 


## Frosty Keypad (Hard)
This one is a little bit more difficult. Hints and elves suggest using an UV light that will reveal the keys pressed by the fingerprint left on them, but this is not given to us as an option. If only we could find one… Well, let’s open up this challenge’s javascript code in Devtools and look for any hints for the flashlight. We can see some very promising variables declarations.

<p><img src="{{site.baseurl}}/images/sans2024/image055.png"></p>
 
The isFollowing variable is however set to false from the very beginning.
Also make a note of the maxDigits = 5 variable. This one defines the max length of the password, even though one can submit more, it’s trimmed down to just 5 when submitting to the server. 
As we explore the code where these variables are used we notice that inside the create function, uvLight object is defined but its visible status is set to disabled.
 
<p><img src="{{site.baseurl}}/images/sans2024/image057.png"></p>
 
Inside devtools’s debugger we find the code and set a breakpoint either on it and step into to allow to initialize. After than we go into the console and set it to true.
 
<p><img src="{{site.baseurl}}/images/sans2024/image059.png"></p>
 
And resume execution of the script. The UV flashlight appears and by selecting it and dragging it over the keypad we can see that fingerprints appear over the 2, 6, 7, 8 and Enter keys. 

<p><img src="{{site.baseurl}}/images/sans2024/image062.png"></p>
 
Realistically speaking, we didn’t even need to do that as the code inside function checkOverlap() reveals which keys would be flagged up anyway.

<p><img src="{{site.baseurl}}/images/sans2024/image063.png"></p>

Well… that doesn’t give us a whole lot of more clues that we didn’t know from before. The previous passcode contained the same keys. It’s a good thing that we know the password is 5 digits and only contains a combination of 4 digits. We’ll have to brute-force it somehow.
I had to get a function to generate all possible 5 digit combinations of the 4 known keys used. There are 1024 in total combinations so we need to get them all and submit to the server for validation.
 
Onto the first quest -> generating all possible combinations with the help of Chat-GPT, I get the following javascript code to run inside the javascript debugger’s console:

<p><img src="{{site.baseurl}}/images/sans2024/image066.png"></p>
 
First, I define an array variable that will contain all possible combinations. 
Then, I define a function that’s going to generate them all possible combinations and call it.
Second, we need to submit these combinations to the server for validation. Normally this happens via a POST request send via the “submitAction” function on line 369. This function receives a callback and the answer itself.

<p><img src="{{site.baseurl}}/images/sans2024/image068.png"></p>

Now this is where things get hairy and surely there might be a much cleaner way of doing this, but this worked for me. 
I submitted one bogus key combination to get all possible variables initialized. 
Then I defined my very own modified version of the same function calling it “runmf” inside devtool’s console. Notice it lost the callback as it’s unnecessary.

<p><img src="{{site.baseurl}}/images/sans2024/image069.png"></p>

I also had to add a function to delay the requests as bombarding the server gets me lots of 429 responses and wouldn’t work. So, we add a delay of 5 seconds between attempts using the below helper function and the sendData function which accepts our array as input.
 
<p><img src="{{site.baseurl}}/images/sans2024/image071.png"></p>
 
Finally, we call the sendData function passing the myStringArray variable. 

<p><img src="{{site.baseurl}}/images/sans2024/image073.png"></p>

We sit back and monitor the progress of the brute-force attempts in Devtools’ network tab.

<p><img src="{{site.baseurl}}/images/sans2024/image076.png"></p>

The moment we get a 200 response is when the combination is accepted. 
The key is “22786”. Success! 



