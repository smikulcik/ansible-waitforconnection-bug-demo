Demo of ansible `wait_for_connection` bug
---

The problem occurs when you try to use `wait_for_connection`
when the host is hidden behind a bastion.

`wait_for_connection` currently uses the `transport_test` method 
the connection script (ssh.py, paramiko_ssh.py ...).  This method
uses `socket.create_socket` to test if the transport is working.

Once a node is hidden behind a bastion, a socket will not be able to 
reach the private network where the node is.

```
  Machine running           
    ansible        <==ssh====> Bastion <===ssh====> hiddenNode
```


To demonstrate
---

We have a two machine vagrant setup.

One machine acts as the bastion, the other the hidden node.

The hidden node has `ufw` firewall rules set so that nothing may
access port 22 on the hidden node except the bastion.

We may ssh into the hidden node using a jump host specified in 
the `ssh_config` file:
```
ssh -F ssh_config hidden
```

However, `vagrant ssh hidden` does not work since it is locked down.


We also have an ansible playbook, `play.yml`, that uses the
`wait_for_connection` module:

1. it tests connectivity with `ping`
2. it runs `sudo shutdown -r now`
3. it waits for the machine to come back up with `wait_for_connection`
4. it tests connectivity again.

However, it will hang on step 3 and eventually timeout since the socket
could not be created.


Steps:
---

1. `vagrant up` - setup the test scenario
2. `ansible-playbook -i hosts play.yml -vvv` - run the playbook and show
   some debug information

