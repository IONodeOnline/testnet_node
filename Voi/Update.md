New update of the AVM (3.21.0) has been released, which introduces dynamic round times, as well as additional AVM opcodes.
 
Updating is required for future growth and stability of the network, and may in the future impact weekly drops.

#### D13 setup upgrade

      sudo apt update
      sudo systemctl stop voi
      sudo apt install algorand
      sudo systemctl start voi
      goal version -v
      goal node status

If you have followed the D13 guide your system may autoupdate at around 6am local time, as configured on your participation node (systemctl cat apt-daily-upgrade.timer | grep OnCalendar)

#### A1CN upgrade

Aust is currently working on upgrading to the latest version, and expecting to have this ready within the next 24 hours.
Release will be up on https://github.com/AustP/austs-one-click-node/releases when ready.





