## Notes on node crashes:
1. When your node crashes with a stack overflow, you must kill the cometbft process manually. For example find the process id with ```bash ps aux | grep cometbft ``` and then run ```bash kill -9 <pid> ```
2. Then increase the file limit with ```bash ulimit -s 65520 ``` and restart the node.
3. You don't need to delete your DB directory.
