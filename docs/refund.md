# Transaction refunds

Atomic swaps work because if any party does not comply with the previously agreed upon terms, the transaction can revert using a refund mechanism.

## Bitcoin based networks

#### Here are some examples of transaction refunds.

### I Initial transaction refund

Alice created and published such transaction:

    {'contract': '63a820b02f390cf0c6d445f8f3a730b2e9fce5dff10da4b47a523d2fd7bf73d8a7a7028876a91485c0522f6e23beb11cc3d066cd20ed732648a4e66704b75db25ab17576a914621f617c765c3caa5ce1bb67f6a3e51382b8da296888ac',
     'contract_address': 'M93y8ouRm8YVez5RBnZi779tgBDPwbdehB',
     'contract_transaction': '01000000015322ad6edd25cc9f2f7dbec73b4d1b123b8abe7fc0e4acb1ec6bf112e1b5b9a5010000008b483045022100c4efbe3a31061df474ec8230428b6f615bc97c0e625a747f22eeeffac6efdbce02200946d6638d7d68086ba478c2280d6669934d63f09233482afcd8634937af63b701410431ab07973bbb5dbc6b7422fc7322abb5df15f77694c0b15b09a325996af47ddd887c7eaa72c656a71fcb333068956de7b3e2f15deaafc1d9285d779ca1b6a3f6000000000290ca04000000000017a9140ca29b3066cde980ffe06268f8bf1902f57ab4dd878f5c0f00000000001976a914621f617c765c3caa5ce1bb67f6a3e51382b8da2988ac00000000',
     'fee': 0.00055184,
     'fee_per_kb': 0.00215564,
     'fee_per_kb_text': '0.00215564 LTC / 1 kB',
     'fee_text': '0.00055184 LTC',
     'locktime': datetime.datetime(2018, 3, 21, 14, 27, 19, 372402),
     'recipient_address': 'LXRAXRgPo84p58746zaBXUFFevCTYBPxgb',
     'refund_address': 'LUAn5PWmsPavgz32mGkqsUuAKncftS37Jq',
     'secret': '376d4e7a476d576b70554a57504f32676569727654687463574a44356e664d72314d35744b50637743374f7a777a436257366c76514541497442316d66764733',
     'secret_hash': 'b02f390cf0c6d445f8f3a730b2e9fce5dff10da4b47a523d2fd7bf73d8a7a702',
     'size': 256,
     'size_text': '256 bytes',
     'transaction_address': 'abbcd57c53576985783c91f5b9cf2109c5a1f16cdfb35480e14a832fb78bfeb7',
     'value': 0.00314,
     'value_text': '0.00314000 LTC'}

She was waiting for the response from Bob for a day but she did not receive any,
so she decided to refund the money from the blockchain:

### 1. Get a wallet from the newtork

    from clove.network import Litecoin

    alice_ltc_wallet = Litecoin.get_wallet(private_key='aliceprivatekey') # provide real key
    alice_ltc_wallet.address
    'LUAn5PWmsPavgz32mGkqsUuAKncftS37Jq'

### 2. Create refund transaction

    from clove.network.bitcoin.contract import BitcoinContract

    ltc_network = Litecoin()

    alice_contract = BitcoinContract(
        network=ltc_network,
        contract='63a820b02f390cf0c6d445f8f3a730b2e9fce5dff10da4b47a523d2fd7bf73d8a7a7028876a91485c0522f6e23beb11cc3d066cd20ed732648a4e66704b75db25ab17576a914621f617c765c3caa5ce1bb67f6a3e51382b8da296888ac',
        raw_transaction='01000000015322ad6edd25cc9f2f7dbec73b4d1b123b8abe7fc0e4acb1ec6bf112e1b5b9a5010000008b483045022100c4efbe3a31061df474ec8230428b6f615bc97c0e625a747f22eeeffac6efdbce02200946d6638d7d68086ba478c2280d6669934d63f09233482afcd8634937af63b701410431ab07973bbb5dbc6b7422fc7322abb5df15f77694c0b15b09a325996af47ddd887c7eaa72c656a71fcb333068956de7b3e2f15deaafc1d9285d779ca1b6a3f6000000000290ca04000000000017a9140ca29b3066cde980ffe06268f8bf1902f57ab4dd878f5c0f00000000001976a914621f617c765c3caa5ce1bb67f6a3e51382b8da2988ac00000000'
    )

    refund_transaction = alice_contract.refund(alice_ltc_wallet)
    RuntimeError: This contract is still valid! It can't be refunded until 2018-03-21 14:27:19 UTC.

Oops! Something went wrong! Contract is still valid! Alice was a bit impatient, wasn't she?

Initial transaction contract is set to be **valid for 48 hours**.

Okey! It is past 2018-03-21 14:27:19 UTC, so Alice is free to create a refund transaction!

    refund_transaction = alice_contract.refund(alice_ltc_wallet)

### 3. Sign and publish refund transaction

    refund_transaction.add_fee_and_sign()

    refund_transaction.show_details()
    {'fee': 0.00069549,
     'fee_per_kb': 0.00217341,
     'fee_per_kb_text': '0.00217341 LTC / 1 kB',
     'fee_text': '0.00069549 LTC',
     'recipient_address': 'LUAn5PWmsPavgz32mGkqsUuAKncftS37Jq',
     'size': 320,
     'size_text': '320 bytes',
     'transaction': '0100000001b7fe8bb72f834ae18054b3df6cf1a1c50921cfb9f5913c78856957537cd5bcab00000000eb4830450221008a1974702c78cdf9067af92d57643641b9b99388fa86a35ab6121348f75b2f7a02202cff4e470e3088fbbde26c71150641428511dd6188e9fdd4ec29f507bde8f50401410431ab07973bbb5dbc6b7422fc7322abb5df15f77694c0b15b09a325996af47ddd887c7eaa72c656a71fcb333068956de7b3e2f15deaafc1d9285d779ca1b6a3f6004c5d63a820b02f390cf0c6d445f8f3a730b2e9fce5dff10da4b47a523d2fd7bf73d8a7a7028876a91485c0522f6e23beb11cc3d066cd20ed732648a4e66704b75db25ab17576a914621f617c765c3caa5ce1bb67f6a3e51382b8da296888ac0000000001e3ba0300000000001976a914621f617c765c3caa5ce1bb67f6a3e51382b8da2988acb75db25a',
     'transaction_address': 'e42d94615792f60eab5503d7dc05a80d9cd394f958af83c8af862af1246e049d',
     'value': 0.00314,
     'value_text': '0.00314000 LTC'}

     refund_transaction.publish()

### 4. Voilà! Alice should get her litecoins back. You can check it for example in a blockcypher.


### II Participate transaction refund

Bob created and published such participating transaction:

    {'contract': '63a820b02f390cf0c6d445f8f3a730b2e9fce5dff10da4b47a523d2fd7bf73d8a7a7028876a9141a376f6634e41c22b28bc9ef3336a623717083a467047d0eb15ab17576a9142b6a3314e8fcf1f1fd6b4d70b112bd5a192850576888ac',
     'contract_address': '3F5em2T4pJd5ParPjs2QenpGL7QDvK2Wyc',
     'contract_transaction': '0100000001daee049a9f9822743a0691ae4f51768e3b4431f5f6abe08da8d8f7afd048e6ff010000008b483045022100c316eb713d282337807de04cdbd67327d78b5deb850f831f0f9aa5c06aa6ab7902203777ed3b22ab88927b92a70b621f44342a6159f10f5a3f456a9d8393e06e9e440141044fbe9cf6ef9bf4a13a693ee1d431eb700a592e8097619e0cfe82aff2a5c231e7154e464e4ec94201007b403b6de9a5819b0bc31eef0741c4fe6e932bca6d9cca0000000002405dc6000000000017a91492e189107196d882ed245bf224940a21a23c5c0187c0fb6e0c000000001976a9142b6a3314e8fcf1f1fd6b4d70b112bd5a1928505788ac00000000',
     'fee': 0.001,
     'fee_per_kb': 0.0,
     'fee_per_kb_text': '0.00000000 MONA / 1 kB',
     'fee_text': '0.00100000 MONA',
     'locktime': datetime.datetime(2018, 3, 20, 14, 37, 1, 516711),
     'recipient_address': 'MAHnD7u7JD4DPA3R267zcB1xbaaiZrDRmL',
     'refund_address': 'MBriWYyfWNdrAmycN5otoUDWDMrdFK33DQ',
     'secret': '',
     'secret_hash': 'b02f390cf0c6d445f8f3a730b2e9fce5dff10da4b47a523d2fd7bf73d8a7a702',
     'size': 256,
     'size_text': '256 bytes',
     'transaction_address': '5d920e7093b2f0ac94cb1c13a42a79ed1c1290fcc4155d15a123d69b1afe05d2',
     'value': 0.13,
     'value_text': '0.13000000 MONA'}

Alice didn't redeem the transaction, so no secret key was published. Bob needed to get his monacoins back.

The process is parallel as in the [initial transaction](#I-Initial-transaction-refund).

### 1. Get a wallet from the newtork

    from clove.network import Monacoin

    bob_mona_wallet = Monacoin.get_wallet(private_key='bobprivatekey') # provide real key
    bob_mona_wallet.address
    'MBriWYyfWNdrAmycN5otoUDWDMrdFK33DQ'

### 2. Create refund transaction

    from clove.network.bitcoin.contract import BitcoinContract

    mona_network = Monacoin()

    bob_contract = BitcoinContract(
        network=mona_network,
        contract='63a820b02f390cf0c6d445f8f3a730b2e9fce5dff10da4b47a523d2fd7bf73d8a7a7028876a9141a376f6634e41c22b28bc9ef3336a623717083a467047d0eb15ab17576a9142b6a3314e8fcf1f1fd6b4d70b112bd5a192850576888ac',
        raw_transaction='0100000001daee049a9f9822743a0691ae4f51768e3b4431f5f6abe08da8d8f7afd048e6ff010000008b483045022100c316eb713d282337807de04cdbd67327d78b5deb850f831f0f9aa5c06aa6ab7902203777ed3b22ab88927b92a70b621f44342a6159f10f5a3f456a9d8393e06e9e440141044fbe9cf6ef9bf4a13a693ee1d431eb700a592e8097619e0cfe82aff2a5c231e7154e464e4ec94201007b403b6de9a5819b0bc31eef0741c4fe6e932bca6d9cca0000000002405dc6000000000017a91492e189107196d882ed245bf224940a21a23c5c0187c0fb6e0c000000001976a9142b6a3314e8fcf1f1fd6b4d70b112bd5a1928505788ac00000000'
    )

    refund_transaction = bob_contract.refund(bob_mona_wallet)
    RuntimeError: This contract is still valid! It can't be refunded until 2018-03-20 14:37:01 UTC.

Oops! Something went wrong! Contract is still valid! Bob was a bit impatient, wasn't he?

Participate transaction contract is set to be **valid for 24 hours**.

Okey! It is past 2018-03-20 14:37:01 UTC, so Bob is free to create a refund transaction!

    refund_transaction = bob_contract.refund(bob_mona_wallet)

### 3. Sign and publish refund transaction

    refund_transaction.fee = 0.001  # As monacoin is not in the blockcypher we need to add fee manually
    refund_transaction.add_fee_and_sign()

    {'fee': 0.001,
     'fee_per_kb': 0.0,
     'fee_per_kb_text': '0.00000000 MONA / 1 kB',
     'fee_text': '0.00100000 MONA',
     'recipient_address': 'MBriWYyfWNdrAmycN5otoUDWDMrdFK33DQ',
     'size': 319,
     'size_text': '319 bytes',
     'transaction': '0100000001d205fe1a9bd623a1155d15c4fc90121ced792aa4131ccb94acf0b293700e925d00000000ea4730440220112731937e6e0a0f5c366e667961ba8d41bc6fd980055c0c911f6dff3cb857d0022033751626806881058120118915a0a7f20280f53952460fff0e2b4c547cc5a7690141044fbe9cf6ef9bf4a13a693ee1d431eb700a592e8097619e0cfe82aff2a5c231e7154e464e4ec94201007b403b6de9a5819b0bc31eef0741c4fe6e932bca6d9cca004c5d63a820b02f390cf0c6d445f8f3a730b2e9fce5dff10da4b47a523d2fd7bf73d8a7a7028876a9141a376f6634e41c22b28bc9ef3336a623717083a467047d0eb15ab17576a9142b6a3314e8fcf1f1fd6b4d70b112bd5a192850576888ac0000000001a0d6c400000000001976a9142b6a3314e8fcf1f1fd6b4d70b112bd5a1928505788ac7d0eb15a',
     'transaction_address': '3582597e1c30759027d9e53843f79528e0f4f0ddc458449f9bb824dabb53ba14',
     'value': 0.13,
     'value_text': '0.13000000 MONA'}

     refund_transaction.publish()
     '3582597e1c30759027d9e53843f79528e0f4f0ddc458449f9bb824dabb53ba14'

### 4. Voilà! Bob should get his monacoins back. You can check it for example [here](https://bchain.info/MONA/addr/MBriWYyfWNdrAmycN5otoUDWDMrdFK33DQ).

## Ethereum based networks

### I Initial transaction refund

Alice created and published such transaction:

    >>> initial_transaction.show_details()
    {'nonce': 249,
    'gasprice': 20000000000,
    'to': '0x7657ca877fac31d20528b473162e39b6e152fd2e',
    'value': Decimal('1000'),
    'data': '0x52f50db7000000000000000000000000000000000000000000000000000000005b59efd0f1281d5c4b4b49ac582d5ee5cd43b871309e0b28000000000000000000000000000000000000000000000000d867f293ba129629a9f9355fa285b8d3711a909200000000000000000000000053e546387a0d054e7ff127923254c0a679da6dbf00000000000000000000000000000000000000000000003635c9adc5dea00000',
    'v': 27,
    'r': 68640564608942029631378553532061187867651305976388122732310692205381648163723,
    's': 42842279473037306433100096957396035599316452268945287098600421922947059503385,
    'sender': '0x999f348959e611f1e9eab2927c21e88e48e6ef45', '
    transaction_address': '0x209a08f2faaea4c6871cd4947293fa3fada5835e1f589fa7f0ab875a858104af',
    'gas_limit': 300000,
    'transaction': '0xf9010b81f98504a817c800830493e0947657ca877fac31d20528b473162e39b6e152fd2e80b8a452f50db7000000000000000000000000000000000000000000000000000000005b59efd0f1281d5c4b4b49ac582d5ee5cd43b871309e0b28000000000000000000000000000000000000000000000000d867f293ba129629a9f9355fa285b8d3711a909200000000000000000000000053e546387a0d054e7ff127923254c0a679da6dbf00000000000000000000000000000000000000000000003635c9adc5dea000001ba097c12ecc5769576b447344c87e687c503390ba2e4003a209a3c4fb322b58a38ba05eb7def14f000a1ee940ffb22417b94e5ad303611e789ff438d70addc8b15119', 'recipient_address': '0xd867f293Ba129629a9f9355fa285B8D3711a9092',
    'value_text': '1000.000000000000000000 BBT',
    'secret': 'fb2cc1d75a54c2fd7d120f307442ad1051157b4759fbb92c3656e8bb639cd909',
    'secret_hash': 'f1281d5c4b4b49ac582d5ee5cd43b871309e0b28',
    'locktime': datetime.datetime(2018, 7, 26, 15, 59, 12, 831884),
    'sender_address': '0x999F348959E611F1E9eab2927c21E88E48e6Ef45',
    'contract_address': '0x7657Ca877Fac31D20528B473162E39B6E152fd2e',
    'refund_address': '0x999F348959E611F1E9eab2927c21E88E48e6Ef45',
    'token_address': '0x53E546387A0d054e7FF127923254c0a679DA6DBf'}

She was waiting for the response from Bob for a day but she did not receive any,
so she decided to refund the money from the blockchain:

### 1. Get a wallet from the newtork

    from clove.network import EthereumTestnet

    >>> alice_eth_testnet_wallet = EthereumTestnet.get_wallet(private_key='aliceprivatekey')
    >>> alice_eth_testnet_wallet.address
    '0x999F348959E611F1E9eab2927c21E88E48e6Ef45'

### 2. Create refund transaction

    >>> from clove.network import EthereumTestnet

    >>> eth_testnet = EthereumTestnet()
    >>> alice_contract = eth_testnet.audit_contract(
        '0x209a08f2faaea4c6871cd4947293fa3fada5835e1f589fa7f0ab875a858104af' // Put your initial transaction address
    )
    >>> alice_contract.show_details()
    {'contract_address': '0x7657Ca877Fac31D20528B473162E39B6E152fd2e',
    'locktime': datetime.datetime(2018, 7, 26, 15, 59, 12),
    'recipient_address': '0xd867f293Ba129629a9f9355fa285B8D3711a9092',
    'refund_address': '0x999F348959E611F1E9eab2927c21E88E48e6Ef45',
    'secret_hash': 'f1281d5c4b4b49ac582d5ee5cd43b871309e0b28',
    'transaction_address': '0x209a08f2faaea4c6871cd4947293fa3fada5835e1f589fa7f0ab875a858104af',
    'value': Decimal('1000'),
    'value_text': '1000.000000000000000000 BBT',
    'token_address': '0x53E546387A0d054e7FF127923254c0a679DA6DBf'}

    >>> refund = alice_contract.refund()

Please remember, that initial transaction contract is set to be **valid for 48 hours**. Before that time, you will not be able to refund your transaction.

### 3. Sign and publish refund transaction

    >>> refund.sign('aliceprivatekey')
    >>> refund.publish()
    '0xbf13af6b32a0af2406fdfb23705742d2d1d097742af9bf3946029e8d1f3feba3'

### 4. Voilà! Alice should get her ethereum_testnet coins back. You can check it for example [here](https://kovan.etherscan.io/tx/0xbf13af6b32a0af2406fdfb23705742d2d1d097742af9bf3946029e8d1f3feba3).



### II Participate transaction refund

Bob created and published such participating transaction:

    >>> participate_transaction.show_details()
    {'nonce': 113,
    'gasprice': 1000000000,
    'to': '0x9f7e5402ed0858ea0c5914d44b900a42c89547b8',
    'value': Decimal('0.5'),
    'data': '0xeb8ae1ed000000000000000000000000000000000000000000000000000000005b5adcbc8cebcb1af6fa5fddeb091f61f0af1c49a6de9922000000000000000000000000000000000000000000000000999f348959e611f1e9eab2927c21e88e48e6ef45',
    'v': 28,
    'r': 45709055192944493283238091426882972043846668244047220959455715247392544872489,
    's': 32702391840690720592637798834593583073452964948638155547821984857632864140453,
    'sender': '0xd867f293ba129629a9f9355fa285b8d3711a9092',
    'transaction_address': '0xe1de0d46e5f62f8d17a2b6dbe9ad0f2937b6c513a91e0be71a2841e8da48d602',
    'gas_limit': 66221,
    'transaction': '0xf8d171843b9aca00830102ad949f7e5402ed0858ea0c5914d44b900a42c89547b88806f05b59d3b20000b864eb8ae1ed000000000000000000000000000000000000000000000000000000005b5adcbc8cebcb1af6fa5fddeb091f61f0af1c49a6de9922000000000000000000000000000000000000000000000000999f348959e611f1e9eab2927c21e88e48e6ef451ca0650e688ea34735d0443766a0a824ba8a932bd9876f914eb8d777da6437455829a0484ce5d993a945cd3f8dc339a161694e97ff0be0483face8d83c0a46bfc6fca5',
    'recipient_address': '0x999F348959E611F1E9eab2927c21E88E48e6Ef45', '
    value_text': '0.500000000000000000 ETH',
    'secret': '',
    'secret_hash': '8cebcb1af6fa5fddeb091f61f0af1c49a6de9922',
    'locktime': datetime.datetime(2018, 7, 27, 8, 50, 4, 937035),
    'sender_address': '0xd867f293Ba129629a9f9355fa285B8D3711a9092',
    'contract_address': '0x9F7e5402ed0858Ea0C5914D44B900A42C89547B8',
    'refund_address': '0xd867f293Ba129629a9f9355fa285B8D3711a9092'}

Alice didn't redeem the transaction, so no secret key was published. Bob needed to get his tokens back.

The process is parallel as in the initial transaction.

### 1. Get a wallet from the newtork

    >>> from clove.network import EthereumTestnet

    >>> bob_eth_testnet_wallet = EthereumTestnet.get_wallet(private_key='bobprivatekey')
    >>> bob_eth_testnet_wallet.address
    '0xd867f293Ba129629a9f9355fa285B8D3711a9092'

### 2. Create refund transaction

    >>> from clove.network import EthereumTestnet

    >>> eth_testnet = EthereumTestnet()
    >>> bob_contract = eth_testnet.audit_contract(
        '0xe1de0d46e5f62f8d17a2b6dbe9ad0f2937b6c513a91e0be71a2841e8da48d602' // Put your participate transaction address
    )
    >>> bob_contract.show_details()
    {'contract_address': '0x9F7e5402ed0858Ea0C5914D44B900A42C89547B8',
    'locktime': datetime.datetime(2018, 7, 27, 8, 50, 4),
    'recipient_address': '0x999F348959E611F1E9eab2927c21E88E48e6Ef45',
    'refund_address': '0xd867f293Ba129629a9f9355fa285B8D3711a9092',
    'secret_hash': '8cebcb1af6fa5fddeb091f61f0af1c49a6de9922',
    'transaction_address': '0xe1de0d46e5f62f8d17a2b6dbe9ad0f2937b6c513a91e0be71a2841e8da48d602',
    'value': Decimal('0.5'),
    'value_text': '0.500000000000000000 ETH'}


    >>> refund = bob_contract.refund()

Please remember, that participate transaction contract is set to be **valid for 24 hours**. Before that time, you will not be able to refund your transaction.

### 3. Sign and publish refund transaction

    >>> refund.sign('bobprivatekey')
    >>> refund.publish()
    '0x28c10e6d0632fb433f6ca7c7fb84db222e4d2ccac39112c91e911c465f21f863'

### 4. Voilà! Bob should get his tokens coins back. You can check it for example [here](https://kovan.etherscan.io/tx/0x28c10e6d0632fb433f6ca7c7fb84db222e4d2ccac39112c91e911c465f21f863).
