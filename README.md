# Monero Ban List

A banlist for Monero nodes.

These nodes were found displaying behaviour that the normal Monero nodes would not do. The code and the method that was used 
to tell these nodes apart is here: https://github.com/Boog900/p2p-proxy-checker. Some nodes are now hiding this fingerprint,
you can see the network state with the monitor by @Rucknium here: https://moneronet.info/.

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
| SyntheticBird | <https://gist.github.com/SyntheticBird45/fd48b3bded266159cc0981c61f0e93f3>                                            |
| hinto-janai   | <https://github.com/Cuprate/cuprate/blob/049cec184f8d46d2fad3274a041ff5e72eabcb73/misc/gpg_keys/hinto-janai.asc>      |

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
