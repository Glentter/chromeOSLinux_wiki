Keep track of your various chroots and ways of launching them by creating _alises_ in ChromeOS's .bashrc file. You will need to learn a bit about using vi, but you can get by with i, a, esc, and :wq. You can access yours with the following command:

`vi ~/.bashrc`

Here is what mine looks like:  

    # /etc/skel/.bashrc  
    #  
    # This file is sourced by all *interactive* bash shells on startup,  
    # including some apparently interactive shells such as scp and rcp  
    # that can't tolerate any output.  So make sure this doesn't display  
    # anything or bad things will happen !
    
    # Test for an interactive shell.  There is no need to set anything  
    # past this point for scp and rcp, and it's important to refrain from  
    # outputting anything in those cases.  

    if [[ $- != *i* ]] ; then  
            # Shell is non-interactive.  Be done now!  
        return  
    fi  
    
    # Put your fun stuff here.  
    
    alias trusty-lxde-xorg="sudo startlxde -b -n trusty -X xorg"  
    alias trusty-lxde-xiwi="sudo startlxde -b -n trusty -X xiwi-tab"

This is really cool, because sudo is included in the command. So can launch:

`trusty-lxde-xorg`

or

`trusty-lxde-xiwi`