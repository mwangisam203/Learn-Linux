# 1. Symlink
ln -s original.txt soft.txt

# 2. Hard link
ln original.txt hard.txt

# 3. Inspect — inode numbers, and note soft.txt shows "->" pointing to original.txt,
# plus its own tiny size and 'l' permission bit (lrwxrwxrwx), unlike hard.txt
ls -li original.txt soft.txt hard.txt

# 4. Both should show "changed" since they all point to the same data
echo "changed" >> original.txt
cat soft.txt
cat hard.txt

# 5. Delete the original
rm original.txt
cat hard.txt   # still works — hard link kept the data alive
cat soft.txt   # fails — "No such file or directory", link is now broken

# 6. Symlink to a directory
ln -s practice/ practice_link
cd practice_link
ls
cd ..

# 7. Find broken symlinks (-xtype l finds links whose target doesn't exist)
find . -xtype l