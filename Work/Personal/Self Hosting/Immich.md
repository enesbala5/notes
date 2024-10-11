
./immich-go -server=http://192.168.0.40:2283 -key=sB2a3Xg4ec6X9yNVragxUCZGcijQfe0xtptfPH5Uiw upload -create-albums -google-photos /home/e/backup/photos/

./immich-go -server=http://192.168.0.40:2283 -key=sB2a3Xg4ec6X9yNVragxUCZGcijQfe0xtptfPH5Uiw duplicate -yes


cat *.tar.gz.* | zcat | tar xvf - -C /home/e/backup/photos2