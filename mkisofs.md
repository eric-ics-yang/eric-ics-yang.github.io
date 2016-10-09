###制作系统盘
mkisofs -R -v -J -T -o ../test.iso -b isolinux/isolinux.bin -c isolinux/boot.cat -no-emul-boot -boot-load-size 4 -boot-info-table ./


mkisofs -J -r -lsv -V testTools -p "test word" -publisher "test" -o ./nailgun.7.0.iso ./nailgun
