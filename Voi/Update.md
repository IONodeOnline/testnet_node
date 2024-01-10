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

#### FAQ

Q) What is changing?
A) Dynamic round times and new AVM opcodes will be available. For more details: https://github.com/algorand/go-algorand/releases/tag/v3.21.0-stable

Q) When will the consensus protocol update?
A) When 90% of consensus voters has upgraded in a 10,000 block window.






