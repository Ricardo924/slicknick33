#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// Define the Book structure
typedef struct {
    char title[50];
    char author[50];
    char isbn[13];
} Book;

// Define the Library structure
typedef struct {
    Book* books;
    int count;
} Library;

// Function prototypes
void addBook(Library* library, char* title, char* author, char* isbn);
void displayBooks(const Library* library);
Book* searchBook(const Library* library, char* isbn);
void removeBook(Library* library, char* isbn);

// Implement the functions
void addBook(Library* library, char* title, char* author, char* isbn) {
    library->books = realloc(library->books, sizeof(Book) * (library->count + 1));
    strcpy(library->books[library->count].title, title);
    strcpy(library->books[library->count].author, author);
    strcpy(library->books[library->count].isbn, isbn);
    library->count++;
}

void displayBooks(const Library* library) {
    for (int i = 0; i < library->count; i++) {
        printf("Title: %s, Author: %s, ISBN: %s\n", library->books[i].title, library->books[i].author, library->books[i].isbn);
    }
}

Book* searchBook(const Library* library, char* isbn) {
    for (int i = 0; i < library->count; i++) {
        if (strcmp(library->books[i].isbn, isbn) == 0) {
            return &library->books[i];
        }
    }
    return NULL;
}

void removeBook(Library* library, char* isbn) {
    int found = 0;
    for (int i = 0; i < library->count; i++) {
        if (strcmp(library->books[i].isbn, isbn) == 0) {
            found = 1;
        }
        if (found && i < library->count - 1) {
            library->books[i] = library->books[i + 1];
        }
    }
    if (found) {
        library->books = realloc(library->books, sizeof(Book) * (--library->count));
    }
}


int main() {
    Library library;
    library.books = NULL;
    library.count = 0;

    char title[50], author[50], isbn[13];
    int choice;

    do {
        printf("\nLibrary Management System\n");
        printf("1. Add book\n");
        printf("2. Display books\n");
        printf("3. Search book\n");
        printf("4. Remove book\n");
        printf("5. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);
        getchar();  // consume newline character

        switch (choice) {
            case 1:
                printf("Enter book title: ");
                fgets(title, sizeof(title), stdin);
                title[strcspn(title, "\n")] = 0;  // remove trailing newline

                printf("Enter book author: ");
                fgets(author, sizeof(author), stdin);
                author[strcspn(author, "\n")] = 0;  // remove trailing newline

                printf("Enter book ISBN: ");
                fgets(isbn, sizeof(isbn), stdin);
                isbn[strcspn(isbn, "\n")] = 0;  // remove trailing newline

                addBook(&library, title, author, isbn);
                break;
            case 2:
                displayBooks(&library);
                break;
            case 3:
                printf("Enter ISBN of book to search: ");
                fgets(isbn, sizeof(isbn), stdin);
                isbn[strcspn(isbn, "\n")] = 0;  // remove trailing newline

                Book* book = searchBook(&library, isbn);
                if (book != NULL) {
                    printf("Found book: %s\n", book->title);
                } else {
                    printf("Book not found.\n");
                }
                break;
            case 4:
                printf("Enter ISBN of book to remove: ");
                fgets(isbn, sizeof(isbn), stdin);
                isbn[strcspn(isbn, "\n")] = 0;  // remove trailing newline

                removeBook(&library, isbn);
                break;
            case 5:
                printf("Exiting...\n");
                break;
            default:
                printf("Invalid choice. Please try again.\n");
        }
    } while (choice != 5);

    // Free dynamically allocated memory
    free(library.books);

    return 0;
}
