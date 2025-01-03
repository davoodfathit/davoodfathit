from mnemonic import Mnemonic
 from tronpy import Tron
 from tronpy.keys import PrivateKey

 # Recovery phrase (Seed Phrase)
 seed_phrase = "equal lucky laugh write bullet lucky dward aspect trick insect desert street"

 # Convert recovery phrase to private key
 mnemo = Mnemonic("english")
 seed = mnemo.to_seed(seed_phrase)
 private_key = PrivateKey(seed[:32]) # Tron uses the first 32 bytes as the private key

 # Display public address for confirmation
 public_address = private_key.public_key.to_base58check_address()
 print("Public Address:", public_address)

 # Using TronPy to send transactions
 client = Tron()

 # Set transaction information
 source_address = public_address
 destination_address = 'TNbuRLys6RjuaKoo2wdYN5zdzcmSsnW9ta'

 # Inventory check
 balance = client.get_account_balance(source_address) / 1e6 # Convert from SUN to TRX
 if balance > 1: # If the balance is more than 1 TRX, it is enough to pay the fee
     print(f"Balance: {balance} TRX")

     # Calculate transferable balance (after deducting 1 TRX fee)
     transferable_amount = balance - 1 # Subtract 1 TRX for fees

     # Create and sign the transaction
     txn = (
         client.trx.transfer(source_address, destination_address, int(transferable_amount * 1e6)) # convert TRX to SUN
         .build()
         .sign(private_key)
     )

     # Send transaction
     result = txn.broadcast()
     print("Transaction successful!", result)
 otherwise:
     print("Not enough TRX to cover transaction fee.")
