Mon Jan 23 13:38:16 PST 2023
Mon Jan 23 13:38:16 PST 2023
    # Tested working on crouton as of 2014-08-01
    # run this once in chroot / ubuntu to install and set basic awesome configuration

    sudo apt-get install awesome
    echo "exec awesome" > ~/.xinitrc
    mkdir -p ~/.config/awesome
    cp -r /etc/xdg/awesome/* ~/.config/awesome/
    
    # after installing awesome, then issue the following at crosh to add the alias
    # then in the future, just type: startawesome to start in awesome
    # echo "alias startawesome='sudo enter-chroot xinit'" >> ~/.bashrc



