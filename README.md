# Linux-IPC-Message-Queues
Linux IPC-Message Queues

# AIM:
To write a C program that receives a message from message queue and display them

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux message queues API 

### Step 3:

Execute the C Program for the desired output. 

# PROGRAM:

## C program that receives a message from message queue and display them

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/ipc.h>
#include <sys/msg.h>

struct msg_buffer {
    long msg_type;
    char msg_text[100];
};

int main() {
    struct msg_buffer message;
    key_t key;
    int msgid, choice;

    // Generate a unique key
    key = ftok("progfile", 65);
    if (key == -1) {
        perror("ftok");
        exit(1);
    }

    // Create or access message queue
    msgid = msgget(key, 0666 | IPC_CREAT);
    if (msgid == -1) {
        perror("msgget");
        exit(1);
    }

    while (1) {
        printf("\n===== MESSAGE QUEUE MENU =====\n");
        printf("1. Send Message\n");
        printf("2. Receive Message\n");
        printf("3. Delete Message Queue\n");
        printf("4. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);
        getchar(); // consume newline

        switch (choice) {
            case 1:
                message.msg_type = 1;
                printf("Enter message to send: ");
                fgets(message.msg_text, sizeof(message.msg_text), stdin);
                message.msg_text[strcspn(message.msg_text, "\n")] = '\0'; // remove newline

                if (msgsnd(msgid, &message, sizeof(message.msg_text), 0) == -1) {
                    perror("msgsnd");
                } else {
                    printf("Message sent successfully!\n");
                }
                break;

            case 2:
                if (msgrcv(msgid, &message, sizeof(message.msg_text), 0, 0) == -1) {
                    perror("msgrcv");
                } else {
                    printf("Received Message: %s\n", message.msg_text);
                }
                break;

            case 3:
                if (msgctl(msgid, IPC_RMID, NULL) == -1) {
                    perror("msgctl");
                } else {
                    printf("Message queue deleted successfully!\n");
                }
                break;

            case 4:
                printf("Exiting program.\n");
                exit(0);

            default:
                printf("Invalid choice! Please try again.\n");
        }
    }

    return 0;
}
```
## OUTPUT
![alt text](<Screenshot at 2025-10-23 10-13-22.png>)

# RESULT:
The programs are executed successfully.
