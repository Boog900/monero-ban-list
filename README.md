# Monero Ban List

A banlist for Monero nodes.

These nodes were found displaying behaviour that the normal Monero nodes would not do.

The total count of IP addresses hosting nodes showing this behaviour is currently over 1900.

False positives are not possible, some of these IP ranges are the same as LinkingLions: https://b10c.me/observations/06-linkinglion/  
The IPs from LinkingLion were not copied, they were found displaying this weird behaviour.

### Usage

To use download `ban_list.txt` and start `monerod` with `--ban-list /path/to/ban_list.txt` 
or add `ban-list=/path/to/ban_list.txt` to your `bitmonero.conf` file.

### Signatures 

To verify the signatures get our GPG keys from these locations:

| Person        | Location                                                                                                              |
|---------------|-----------------------------------------------------------------------------------------------------------------------|
| boog900       | <https://github.com/Cuprate/cuprate/tree/7b8756fa80e386fb04173d8220c15c86bf9f9888/misc/gpg_keys>                      |
| jeffro256     | <https://github.com/monero-project/monero/blob/004ead1a14d60ff757880c5b16b894b526427829/utils/gpg_keys/jeffro256.asc> |
| Rucknium      | <https://rucknium.me/pgp.txt>                                                                                         |
| SyntheticBird | <https://github.com/Cuprate/cuprate/blob/main/misc/gpg_keys/syntheticbird.asc>                                        |

Then import the keys and verify the signatures:

```bash
gpg --verify ./sigs/boog900.sig ban_list.txt
```

You should now see: 
```
gpg: Signature made Wed Dec  4 23:27:28 2024 GMT
gpg:                using EDDSA key A875F544CB569CB96889791E42AB1287CB0041C2
gpg: Good signature from "Boog900 ...
```

Now repeat with as many `Person`'s signatures as you please.
