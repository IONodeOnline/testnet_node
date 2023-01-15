Run TFSC Node

This article will guide you on how to run the TFSC application on centos7, ubuntu-22.04-desktop-amd64 operation system to join in TFSC network.

Hardware requirements：

CPU: Recommend for Intel Pentium CPU series and above, core number 8 core and above.
Memory: Recommend for 16G And above
Hard Disk: Recommend for 500G with NVME protocol
Bandwidth: 100 megabit upstream and downstream

Software requirements：

Operation system：Run on CentOS 7、ubuntu-22.04-desktop-amd64 system.

Command-line mode

1.Enter the command in the Linux terminal as follows

Download the main network program（ttfsc_0.x.x_dev.zip）, then unpack to get the ttfsc_0.x.x_dev binary executable, and then modify the execution permission of the program.
 
       unzip ttfsc_0.x.x_dev.zip
       chmod +x ttfsc_0.x.x_dev
       ./ttfsc_0.x.x_dev
       
2.To view the automatically generated directories and files under the current directory.

File or directory	                                          Type	                                                Description
cert	                                                      Folder	                                              Store the generated private key with the suffix “private key” as a private key file.
data.db	                                                    Folder	                                              store the database file
config.json	                                                JSON file	                                            configuration file

! TIPS:

Protect your private key

1.The private key in the cert must not be lost.

3.Configuration config.json

If your node wants to be shared with others, you only need to configure the network IP as a public network address IP to ensure that external networks can access your node. If there is no such requirement, you do not need to modify the IP address. The corresponding open port shall be port 41516、port 41506 and port 41517.

4.Program startup prompt box:

      *********************************************************************************
      Version: 1_0.0.1_d
      Base58: 12j1RLDE7mhw61n4m5DsDWg8kb8ESxfcR1
      Balance: 123.45678900
      Block top: 0
      Gas:0.00001078
      *********************************************************************************
      ### 1. Version: Version number of the program 
      ### 2. Base 58: The base58 address of the program
      ### 3. Balance: Balance for the corresponding Base58.
      ### 4. Balance_accuracy: The exact number of Base58 balances.
      ### 5. Block top: The height of the current block.
      ### 6. Gas: fee
      
Notice: Explain the function of the menu.

Order	      Menu item	        Function

1	          Transaction	      Transaction

2	          Stake	            Stake

3	          Unstake	          Disinvest

4	          Invest	          Invest

5         	Disinvest       	Disinvest

6	          Bonus           	apply

7	          PrintAccountInfo	Print account

0         	Exit	            Exit program
Menus

1. Transaction

Press 1 to enter the transaction interface.

1 Enter the account number address of the transaction initiator.

      input FromAddr: 12GwpCQi7bWr8cbmU2r1aFia1rUQJDVXdo

2 Enter the account address of the other party:

      input ToAddr: 1vkS46QffeM4sDMBBjuJBiVkMQKY7Z8Tu   
      
3 Enter the transaction amount, the range here is any number within your account balance range, and the input value is equal to the actual value, namely, the currency's number.

      input amount: 10
      
If successful, there are is below:

      Remove pending transaction in Cache 1d1a3d191787c6b24ad154a0bbef7f07444d1331d3bbd502aa9b871ec9a34591 
      from 115SwrxtYUucetvtgr1M1rKgDyzDLiyre5   

2. Stake
3. 
1 Press 2 to enter the stake interface, and note that the staked account is your own native account:

      Default account： 1FFKXsov2c3Ud9V5moWdPJ35tKZT5SRoAj    
      
2 Enter the amount to be staked.

      Please enter the amount to stake：9.9
      
![image](https://user-images.githubusercontent.com/91251550/212543491-a447eec1-3bf1-4b75-9cf1-32797efdfd54.png)

NOTICE:
Signature conditions: access to the network 9.9 and the node is invested greater than or equal to 5000 (the invested amount can be accumulated)

The stake is completed.

3. Unstake

Press3. Enter the Unstake interface.

1 Enter the Unstake account number.

      Please enter addr：
      12GwpCQi7bWr8cbmU2r1aFia1rUQJDVXdo
      
2 Then indicate that there has been a stake transaction:

      -- Current pledge amount: -- 
      utxo: c8a055a94c216d7a602de4865674c7c12343b8a35f0d0c826041b03ebe96d480
      
3 Enter the transaction to be staked.

      Please enter utxo:c8a055a94c216d7a602de4865674c7c12343b8a35f0d0c826041b03ebe96d480
      
![image](https://user-images.githubusercontent.com/91251550/212543654-5bd4e40d-92c4-4584-ac07-e969d7ac11c2.png)

Complete the Unstake

4. Invest

Press 4 to enter the investment interface, (you can invest in different accounts, or invest in yourself).

Enter the investment account number.

1 Please enter your addr:

      1LPPSqMVaRNUem8QXo42yWWA71HrbVqpcd
      
2 Enter the invested account number.

      Please enter the addr you want to invest to:
      1zzZ9FgrTbiMtkQLN93KeRgvA9Yrxy11K

3 Enter the amount to be invested:

      Please enter the amount to invest:
      500
![image](https://user-images.githubusercontent.com/91251550/212543712-f890c832-e1e3-4a45-a1da-a4dc790bef8f.png)

Complete the investment

5. Disinvest

Press 5 to enter the solution investment interface.

Enter the investment account number：

      Please enter your addr:
      1zzUZ9ZX5fqXEsYs61wRUUdCE85ZmTbkk
      
Enter the invested account number：

      Please enter the addr you want to divest from:
      1LPPSqMVaRNUem8QXo42yWWA71HrbVqpcd
      
      ======================================= 
      Current invest amount: =======================================
      Utxo: 4e1c31b20a40a912067cc6328c8c90ad79e4554ec11b05d56b3296b60db39e55
      ======================================================================================================
      Please enter the utxo you want to divest:
      4e1c31b20a40a912067cc6328c8c90ad79e4554ec11b05d56b3296b60db39e55
![image](https://user-images.githubusercontent.com/91251550/212543754-4a30f19f-5bc6-4f9b-ac9b-18bfd1f76f8d.png)

Complete the Disinvest

6. Bonus

Press 6 to enter the application.

      Application address: Application amount
      Claim Addr : Claim Amount
      1zzUZ9ZX5fqXEsYs61wRUUdCE85ZmTbkk:284794520
      1zzUZ9ZX5fqXEsYs61wRUUdCE85ZmTbkk:14239726
      
![image](https://user-images.githubusercontent.com/91251550/212543785-32ff9a5b-1a36-4b39-87c5-9fdd48d4e7c1.png)

7. PrintAccountInfo

Press 7 to print the account information.

      *********************************************************************************
      Version: 1_0.3.0_d
      Base58: 12j1RLDE7mhw61n4m5DsDWg8kb8ESxfcR1
      Balance: 123.45678900
      Block top: 0
      Gas:0.00001078
      *********************************************************************************
      ### 1. Version: Program version number.
      ### 2. Base58: The base 58 address of the program.
      ### 3. Balance: Balance for the corresponding Base 58.
      ### 4. Balance_accuracy: The exact number of Base 58 balances.
      ### 5. Block top: Current block height.
      ### 6. Gas: fee
      
![image](https://user-images.githubusercontent.com/91251550/212543806-7ec04a28-ac91-440c-a5ff-8075abe95819.png)

Exit
