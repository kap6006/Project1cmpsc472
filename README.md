# Project1cmpsc472
This project aims to develop a system that processes multiple large files in parallel using multiprocessing and multithreading, while employing inter-process communication (IPC) mechanisms for message passing between processes.

## Overview
The C program is designed to count word frequencies in text files within a specified directory, utilizing both single-threaded (using fork()) and multi-threaded (using prthread)

## Project Description
The program implements counting the top 50 words in each file two different ways. One of them is single-threaded processes. In this approach, each file is processed by a separate child process that counts words sequentially (single-threaded within the process). The parent process spawns a child process for each file, and each child performs the word count on the entire file. The fork() system call is used to create a new process (child process) for each file. After the fork, the child process is responsible for reading the file and counting words. The child process uses the function count_word_frequency_single() to read the file and count the occurrences of each word. This function scans the file word by word, converts each word to lowercase, checks if the word is already in the list of counted words, and either increments the count or adds the word to the list. The child process does this in a single-threaded manner, meaning it processes the file sequentially, without dividing the work among multiple threads. One advantage to this approach is that it is simpler to implement as there is no need for synchronization mechanisms like mutexes since there is only one logical thread. Another advantage is that each process has its own memory space, which reduces race conditions. A disadvantage is the higher overhead due to creating and managing multiple processes since forking is more expensive than creating threads. Context switching between processes is also slower compared to threads.

For the other approach, a single process is created (either by the parent process or as a child process), and multiple threads are created within that process to count words in parallel. The file is either logically divided into parts, and each thread handles a part of the file, or all threads can work on the file simultaneously. The program uses the POSIX threads (pthreads) library to create multiple threads within a process. Each thread can be assigned a portion of the file to process or can work collaboratively to count words. One advantage is the lower overhead than creating new processes since thread creation is faster. Another advantage is that multiple threads can share memory within a process, which can reduce memory usage. However, multiple threads require careful management of shared data so you need synchronization mechanisms like mutexes. Another disadvantage is it is limited by the number of CPU cores available making this approaches effectiveness hardware dependant.

## Project Requirements
### Process management

### IPC mechanism
In the program, a pipe is created using the pipe() system call, which provides two file descriptors:
pipe_fd[0]: Used for reading from the pipe.
pipe_fd[1]: Used for writing to the pipe.
Before forking a child process, the parent process creates a pipe. This pipe allows the child process to send the word count results to the parent process. If the pipe creation is successful, the parent process proceeds to fork a child process.
Code Example:
int pipe_fd[2];
if (pipe(pipe_fd) == -1) { // Error checking
    perror("Pipe failed");
    continue;
}
Once the pipe is created, the program uses fork() to create a new child process. After the fork the child process performs the word counting and sends the result to the parent process through the pipe. The parent process waits for the child to finish and reads the result from the pipe.
### Threading
### Error handling
### Performance evaluation

## Discussion
I faced a lot of challenges being able to set up a proper environment to run C code on my windows computer because of the differences between Windows API and Unix-based C code for other systems and I was not able to write proper C code that works for the Windows API in time to finish the requirements. For class we have also mostly been using Google Colab which is very different from running on a local computer. I tried to write a program in Python and found that the code didn't demonstrate enough of what I wanted to see the differences in single-threaded processing and multi-threaded processing or use the specific libraries that were required such as pthreads. Ultimately I had to run the C code in Google Colab which uses Python to compile and run the code and due to Python's nature, it does not run the threads concurrently on different CPU cores. However, while writing the code and testing, I was still able to understand how threading with processes work, although the runtimes did not reflect the design of the program due to it being run in Google Colab. With more time, I would be able to set up a proper code environment to run Unix-based C code, or write code that works with the Windows API. 
