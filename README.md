# Platypus protecting user funds


Last week, as MIM was risking a depeg, Platypus decided to <a href="https://twitter.com/MrBeaverTail/status/1486880374705823751">delist MIM from their platform</a> and paused the MIM pool's contract, preventing LPs from withdrawing. They then proceeded to attack Curve for their "irresponsible" handling of the issue, arguing that they acted in the best interest of their LPs:

![image](https://user-images.githubusercontent.com/98719843/151724588-30d877a5-07f2-4237-9c06-0d5fff31a978.png)

One thing caught my eye in the <a href="https://twitter.com/MrBeaverTail/status/1486880379680272384">Twitter thread</a> from Platypus' lead developer explaining how their decision to withdraw:

![image](https://user-images.githubusercontent.com/98719843/151724599-ef757d2b-ef24-4a1d-ad8c-3f8f910349f9.png)

This means that Platypus sold their excess MIM via other platforms in the midst of market-wide sell pressure on MIM. This got me curious: how did they find a buyer?

Turns out it's quite easy to find out. Using snowtrace we find <a href="https://snowtrace.io/tx/0x87ada964c273830be9f46dfc6e2a2d54cf4ad66878805f6f98a27c3a6262657a">a transaction to pause the Pool's contract</a>, preventing any further trading or liquidity removals. This is immediately followed by <a href="https://snowtrace.io/tx/0xfe003257c55b6b7859ed8946403539c0f7dcde8b3bba1d8b0ec63375d90c18aa">transactions to transfer underlying assets of the pool </a> and calls to "Remove cash". (Note that the address on which "Pause" is called differs from the subsequent addresses where funds are removed. However this is only because the transactions call different proxies, but they all point to the same Platypus MIM pool at <a href="https://snowtrace.io/address/0xe2d3eb21db91f6a8dde5dac9fde7f546aa7590ba#code">0xe2d3eb21db91f6a8dde5dac9fde7f546aa7590ba</a>. 

![image](https://user-images.githubusercontent.com/98719843/151724604-60c07673-2872-4857-899d-feb45c89d7ab.png)

One additional thing the seems worrying is that all these transactions are done by an EOA account. This means that at any time, Platypus pools can be drained by a single user (although, in all fairness, the funds are trasmitted to the Platypus multisig, not the user).

In any case, in one of these transactions we see 85 millions worth of MIM being taken from the pool and transferred to Platypus' multisig:

![image](https://user-images.githubusercontent.com/98719843/151724609-6c857a22-9602-4bcb-8388-03dbf99a58f7.png)

Now going to the multisig, we can find <a href="https://snowtrace.io/tx/0x8a9c42f6f28ea109c925cbb1e55c74b954a519be5d8e7557b7c2e169875de1f2#eventlog">a transaction</a> that transfers these 85 million MIM to an EOA address at 0xdacff5f4f3df1df4e2ff90c6e311469debb086ba:

![image](https://user-images.githubusercontent.com/98719843/151724620-596e9372-40db-454d-8e78-1f784422b074.png)

So $85 million worth of user's funds are now in the hands of a single user. Checking this user's activities, we find that they quickly <a href="https://snowtrace.io/tx/0xf0300f0ea91422ec02b787fe181d43299a13cc1df78b7ee3571424d74b2a61ce#eventlog">use Anyswap to transfer the funds to Ethereum mainnet</a>:
  
![image](https://user-images.githubusercontent.com/98719843/151724646-38b9417d-14d6-49ab-bdc6-f4d126a2e152.png)

This is done in small batches, but the same EOA wallet receives the full amount on the Ethereum side. Switching from Snowtrace to <a href="https://etherscan.io/address/0xdacff5f4f3df1df4e2ff90c6e311469debb086ba">Etherscan</a> we can see that the wallet then <a href="https://etherscan.io/tx/0x7c3faad7f8e7d901244b7e80df43d10b6dc43d4c25f70406f4dd8d60f7570204">approves MIM for spending on CoW Swap<a/>. 

![image](https://user-images.githubusercontent.com/98719843/151724652-da257171-bad8-4876-b4de-1fe7d7b0f9ad.png)


To find where the money went, we need to turn to CowSwap's own explorer. We quickly find the <a href="https://gnosis-protocol.io/orders/0x92de79da25370a4ce857eb302cc94e9fa9f231dba8c1d791c710f236aada56e6dacff5f4f3df1df4e2ff90c6e311469debb086ba61f2c094">track of the trade<a/>:
  
![image](https://user-images.githubusercontent.com/98719843/151724659-4d313c53-db62-47c4-b85b-d36fea44fcf6.png)

So the wallet in charge of Platypus' funds went to Cow Swap and managed to sell their 85,670,806.2 MIM for a total of 85,601,853.8 USDC. This is a great trade, with very little price impact. One can only wonder how was such sorcery possible? The Cow Swap explorer gives us a link to the <a href="https://etherscan.io/tx/0xbafe10f05e5db9aa9efc5d57fc282af9d3bc76e2a4d4394731883698d76a8e48">settlement transaction on Etherscan</a> where we can find the full details:
  
![image](https://user-images.githubusercontent.com/98719843/151724667-ce4bfa5f-11be-489d-9637-16276cba1d6c.png)

Using Curve's Tripool, they dumped their MIM for DAI at almost 1:1, then proceeded to move the funds back to Avalanche.
  
All is well that ends well then, Platypus MIM LPs can now get their refunds via a withdrawal platform on the website, no user funds were lost. All thanks to Curve. Yet instead of expressing gratitude towards the platform that allowed them to save their LP's funds, Platypus went on to spread FUD. 
  
  


