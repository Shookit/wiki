IRC
====

Client setup
------------

```
pacman -S weechat
/mouse enable
/set weechat.plugin.autoload "*,!ruby,!lua,!aspell,!tcl,!guile"
/server add freenode chat.freenode.net
/set irc.server.freenode.autoconnect on
/set irc.server.freenode.autojoin "#centos,#archlinux,#rhel,#python,##linux,##chat,##networking"
/set irc.server.freenode.sasl_username "<username>"
/set irc.server.freenode.sasl_password "<password>"
/set irc.look.smart_filter on
/filter add irc_smart * irc_smart_filter *
```
