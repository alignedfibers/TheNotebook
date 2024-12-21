# Make one image without a table layout just empty
truncate ahidden_flat0 --size 300MB
truncate ahidden_enc_scheme0 --size 300MB
truncate ahidden_enc_scheme1 --size 300MB

#Gives the full byte size of the 300MB image
echo $(( (1 * 1000000 * 300) ))
#Gives the amount of blocks assuming a 512MB block size.
echo $(( (1 * 1000000 * 300) / 512 ))


osetup -o 195312 --sizelimit=100000000 /dev/loop0 ahidden_flat0