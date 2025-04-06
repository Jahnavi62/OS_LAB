# OS_LAB
## Basic Unix Commands
### General Commands
date commands
![Screenshot (44)](https://github.com/user-attachments/assets/183e9a60-8758-4416-82c4-abdcd78df199)


calender commands

![Screenshot (47)](https://github.com/user-attachments/assets/c7cf7313-8eaa-4acb-aef6-ac517f88d3a3)

![Screenshot (48)](https://github.com/user-attachments/assets/df4fb8c9-afdf-422a-80bc-3136c9b1aba1)

![Screenshot (49)](https://github.com/user-attachments/assets/6c8f961b-59ce-49cb-b053-ce7c25b3f83c)

![Screenshot (52)](https://github.com/user-attachments/assets/5df79b2c-a011-45a7-b594-62114acc418d)

![Screenshot (51)](https://github.com/user-attachments/assets/8bb2bd78-07b2-4f8e-bdfb-80024530c9da)

![Screenshot (53)](https://github.com/user-attachments/assets/a3a32124-33cb-403e-91f8-d0319f42b33c)

![Screenshot (54)](https://github.com/user-attachments/assets/25c1e969-986e-4c24-952a-464bd1ca7ac7)

![Screenshot (57)](https://github.com/user-attachments/assets/6e2fc4f5-69ff-4d31-bd81-d54dd8521c65)

![Screenshot (58)](https://github.com/user-attachments/assets/def47fe7-cda1-4527-9548-e1ed97330d83)

![Screenshot (59)](https://github.com/user-attachments/assets/08b68eb5-bc85-45e9-b527-d208839bba76)

![Screenshot (60)](https://github.com/user-attachments/assets/eb54b633-d333-49f3-a2c1-9068346ae9f0)

![Screenshot (151)](https://github.com/user-attachments/assets/2b671f65-6982-45b6-b060-a79f0ae5a4cc)


> ## Simulate UNIX commands like cp, ls, grep, etc
> ### Cp simulate command Source code
```c
#include <stdio.h>
#include <stdlib.h>
void simulate_cp(const char *source, const char *destination) {
FILE *src = fopen(source, "r");
FILE *dest = fopen(destination, "w");
if (!src || !dest) {
perror(!src ? "Error opening source file" : "Error opening destination file");
if (src) fclose(src);
return;
}
char buffer[1024];
size_t bytes;
while ((bytes = fread(buffer, 1, sizeof(buffer), src)) > 0) {
fwrite(buffer, 1, bytes, dest);
}
printf("File '%s' copied to '%s'\n", source, destination);
fclose(src);
fclose(dest);
}
int main(int argc, char *argv[]) {
if (argc != 3) {
fprintf(stderr, "Usage: %s <source_file> <destination_file>\n", argv[0]);
return EXIT_FAILURE;
}
simulate_cp(argv[1], argv[2]);
return EXIT_SUCCESS;
}
```
> ### Simulation code for ls command
```c
#include <stdio.h>
#include <stdlib.h>
#include <dirent.h>
void simulate_ls(const char *path) {
struct dirent *entry;
DIR *dir = opendir(path);
if (!dir) {
perror("Error opening directory");
return;
}
while ((entry = readdir(dir)) != NULL) {
if (entry->d_name[0] != '.') {
printf("%s ", entry->d_name);
}
}
printf("\n");
closedir(dir);
}int main(int argc, char *argv[]) {
const char *path = argc > 1 ? argv[1] : ".";
simulate_ls(path);
return 0;
}
```
> ### Simulate Grep command
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
void simulate_grep(const char *pattern, const char *filename) {
FILE *file = fopen(filename, "r");
if (!file) {
perror("Error opening file");
return;
}
char line[1024];
while (fgets(line, sizeof(line), file)) {
if (strstr(line, pattern)) { // Check if pattern exists in the line
printf("%s", line);
}
}
fclose(file);
}int main(int argc, char *argv[]) {
if (argc != 3) {
fprintf(stderr, "Usage: %s <pattern> <filename>\n", argv[0]);
return EXIT_FAILURE;
}
simulate_grep(argv[1], argv[2]);
return EXIT_SUCCESS;
}
```















