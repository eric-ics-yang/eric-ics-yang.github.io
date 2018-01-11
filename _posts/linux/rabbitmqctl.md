### Users management

Got an error when communicate through rabbitmq, some commands are listed to debug.To [get started with rabbitmq].

  [get started with rabbitmq]: http://www.rabbitmq.com/getstarted.html
  
```
# rabbitmqctl add_user test test123456
# rabbitmqctl list_users
Listing users ...
test
guest   [administrator]

Setting permissions for user "test" in vhost "/" ...
# rabbitmqctl set_permissions -p "/" test ".*" ".*" ".*"

# rabbitmqctl list_permissions -p /
Listing permissions in vhost "/" ...
test .* .* .*
guest .* .* .*

# rabbitmqctl set_user_tags test administrator
Setting tags for user "test" to [administrator] ..

# rabbitmqctl list_users
Listing users ...
test [administrator]
guest [administrator]

#rabbitmqctl list_exchanges

You can list existing bindings using, you guessed it,
#rabbitmqctl list_bindings
# => Listing bindings ...
# => logs    exchange        amq.gen-JzTY20BRgKO-HjmUJj0wLg  queue           []
# => logs    exchange        amq.gen-vso0PVvyiRIL2WoV3i48Yg  queue           []
# => ...done.
```
