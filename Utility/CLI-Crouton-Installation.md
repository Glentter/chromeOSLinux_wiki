Mon Jan 23 13:38:16 PST 2023
As an alternative to the general method, described in the README, you can download `crouton` directly using shell (Ctrl+Alt+T, type `shell` and hit enter) with the following commands:

- Create required target dir and don't print an error if /usr/local/bin/ directory already exists; it's created automatically if you ran `dev_install` for experimenting with ChromeOS development, otherwise you may create it manually:

`sudo mkdir -p /usr/local/bin`

- download the crouton release & & :

`sudo curl -L -s -o /usr/local/bin/crouton https://raw.githubusercontent.com/dnschneid/crouton/master/installer/crouton`

- set the executable permissions for it 

`sudo chmod +x /usr/local/bin/crouton`

- run it once to show help and prove it works

`sudo crouton`

- copy and paste the whole block below to run all these commands automatically together

```
sudo mkdir -p /usr/local/bin && \
sudo curl -L -s -o /usr/local/bin/crouton https://raw.githubusercontent.com/dnschneid/crouton/master/installer/crouton && \
sudo chmod +x /usr/local/bin/crouton && \
sudo crouton
```