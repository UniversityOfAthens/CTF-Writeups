### Challenge: FANTASY CTF
### Difficulty: Easy
#### Prepared by: deathwish24

In order to solve this challenge we need to connect to the given IP and port using `netcat`:

```console
qwerty@forallsecure:~$ nc verbal-sleep.picoctf.net 63285
FANTASY CTF SIMULATION

The simulation begins in the private room of Eibhilin, a bright, young student.
The room is dimly lit, with the glow of her multiple monitors casting an
electric blue hue on the walls. Around the room are posters of vintage movies
from the MCU — ancient guardians from another age staring down like digital
sentinels.

---
(Press Enter to continue...)
---
```

Then we press `Enter` a few more times until we get the following message:

```console
Nyx brings up the registration page.

Options:
A) *Register multiple accounts*
B) *Share an account with a friend*
C) *Register a single, private account*
[a/b/c] > 
```

Here you can choose whatever you want. I will choose `c` since it's the only option, that follows the rules of the competition.

```console
[a/b/c] > c

Nyx nods, "Good choice, Ei. Let's proceed with a single, private account. It
can be tempting to try and do something 'clever', but registering multiple
accounts or sharing an account with a friend can lead to disqualification."

---
(Press Enter to continue...)
---
```

We press `Enter` again for a few times until we get the following message:

```console
Options:
A) *Play the game*
B) *Search the Ether for the flag*
[a/b] >
```

Again since we need to follow the rules of the competition, I will choose `a`.

```console
[a/b] > a

"Good choice, Ei," Nyx says, "You never want to share flags or artifact
downloads."

---
(Press Enter to continue...)
---
```

Here after pressing `Enter` we get a message saying `Playing the Game`:

```console
Playing the Game
Playing the Game: 100%|██████████████████████████████████████ [time left: 00:00]
Playing the Game completed successfully!

---
(Press Enter to continue...)
---
```

Finally we press `Enter` a few more times and it will show us the flag:

```console
END OF FANTASY CTF SIMULATION
Thank you for playing! To reemphasize some rules for this year:
1. Register only one account.
2. Do not share accounts, flags or artifact downloads.
3. Wait to publish writeups publicly until after the organizers announce the
winners.
4. picoCTF{........._......._........} is a real flag! Submit it for some
points in picoCTF 2025!

---
(Press Enter to continue...)
---
```