// z5352187
// Assignment 2 21T3 COMP1511: CS Pizzeria
// pizzeria.c
//
// This program was written by TAN-TAI-TRAN (ANDY TRAN) (z5352187)
// on 11/17/2021
//
// DESCRIPTION: This program allows users to manage and operate a real pizzeria
// which includes almost essential commands to start running a store. You may 
// find some fun from your experience of being a pizzeria owner. Let's start 
// serving your delicious pizzas to the world. ENJOY IT! ^-^
//
// Version 1.0.0: Release

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// TODO: add any extra #includes your code needs here.

#include "pizzeria.h"

// TODO: add your own #defines here.


struct ingredient {
    // TODO: add your own fields in Stage 2.
    char ingredient_name[MAX_STR_LENGTH];
    int amount;
    double price;
    struct ingredient *next;
};

struct order {
    // TODO: add your own fields in Stage 1.
    // Looking to store a price field? Try putting in "double price;" here!
    char customer[MAX_STR_LENGTH];
    double price;
    char pizza_name[MAX_STR_LENGTH];
    int time_allowed;
    struct order *next;
    struct ingredient *add_ingredient;
};

struct pizzeria {
    // TODO: add your own fields if required.
    struct order *selected;
    struct order *orders;
    struct ingredient *stock;
};

// TODO: add any other structs you define here.

//////////////////////////////////////////////////////////////////////

// TODO: add prototypes for any helper functions you create here.

void free_ingredients_ordered (struct ingredient *head);

void stock_update(struct pizzeria *pizzeria);

struct ingredient *add_ingredient_ordered(char *ingredient_name, int amount, 
double price, struct ingredient *head);

int ingredient_checking (struct ingredient *a , struct ingredient *head);

// Prints a single order
void print_order(
    int num,
    char *customer,
    char *pizza_name,
    double price,
    int time_allowed
);

// Prints an ingredient given the name, amount and price in the required format.
// This will be needed for stage 2.
void print_ingredient(char *name, int amount, double price);

////////////////////////////////////////////////////////////////////////
//                         Stage 1 Functions                          //
////////////////////////////////////////////////////////////////////////


struct pizzeria *create_pizzeria() {
    // Allocates memory to store a `struct pizzeria` and returns a pointer to
    // it. The variable `new` holds this pointer!
    struct pizzeria *new = malloc(sizeof(struct pizzeria));
    new->selected = NULL;
    new->orders = NULL;
    new->stock = NULL;

    // TODO: this function has already been implemented for the current
    // struct pizzeria. When you decide to change struct pizzeria, change
    // this function as well.

    return new;
}

int add_order(
    struct pizzeria *pizzeria,
    char *customer,
    char *pizza_name,
    double price,
    int time_allowed
) {
    if (price < 0) {
        return INVALID_PRICE;
    }
    if (time_allowed <= 0) {
        return INVALID_TIME;
    }
    struct order *current = pizzeria->orders;
    struct order *new_order = malloc(sizeof(struct order)); 
    
    strcpy(new_order->customer, customer);
    strcpy(new_order->pizza_name, pizza_name);
    new_order->price = price;
    new_order->time_allowed = time_allowed;
    new_order->next = NULL;
    new_order->add_ingredient = NULL;
    
    if (current == NULL) {
        pizzeria->orders = new_order;
    }
    if (current != NULL) {
        while (current->next != NULL) {
            current = current->next;
        }
        new_order->next = current->next;
        current->next = new_order;
    }
    
    return SUCCESS;
}

void print_all_orders(struct pizzeria *pizzeria) {

    struct order *current = pizzeria->orders;
    int num = 1;
    while (current != NULL) {
        print_order(num, current->customer, current->pizza_name, current->price, 
        current->time_allowed);
        current = current->next;
        num++;
    }
    // Do not change this part of the function
    print_selected_order(pizzeria);
    return;
}

int next_deadline(struct pizzeria *pizzeria) {
    struct order *current = pizzeria->orders;
    int deadline = INVALID_CALL;
    if (current != NULL) {
        deadline = current->time_allowed;
        while (current->next != NULL) {
            if (deadline >= current->next->time_allowed) {
                deadline = current->next->time_allowed;
            }
            current = current->next;
        }
    }
    return deadline;
}

////////////////////////////////////////////////////////////////////////
//                         Stage 2 Functions                          //
////////////////////////////////////////////////////////////////////////


void select_next_order(struct pizzeria *pizzeria) {
    struct order *current = pizzeria->orders;
    if (current == NULL) {
        return;
    }
    struct order *selected = pizzeria->selected;
    // when the selection is not assigned to any values;
    if (selected == NULL) {
        pizzeria->selected = current;
    }
    // when the selection is already assigned to some values;
    if (selected != NULL) {
        if (selected->next == NULL) {
            pizzeria->selected = NULL;
            return;
        }
        // when the selection is reached to the end of the list;
        while (current != selected) {
            current = current->next;
        }
        pizzeria->selected = current->next;
    }
    return;
}

void select_previous_order(struct pizzeria *pizzeria) {
    struct order *current = pizzeria->orders;
    struct order *selected = pizzeria->selected;
    if (current == NULL) {
        return;
    }
    // when the selected has not been assigned any values
    if (selected == NULL) {
        while (current->next != NULL) {
            current = current->next;
        }
        pizzeria->selected = current;
    }
    // when the selected has existed along the orders
    if (selected != NULL) {
        if (selected == current) {
            pizzeria->selected = NULL;
            return;
        }
        while (current->next != selected) {
            current = current->next;
        }
        pizzeria->selected = current;
    }
    return;
}

void print_selected_order(struct pizzeria *pizzeria) {

    if (pizzeria->selected != NULL) {
        struct order *current = pizzeria->selected;
        struct ingredient *ingre = pizzeria->selected->add_ingredient;
        printf("\nThe selected order is %s's %s pizza ($%.2lf) due in %d minutes.\n", 
        current->customer, current->pizza_name, current->price, 
        current->time_allowed);
        
        if (ingre != NULL) {
            while (ingre != NULL) {
                print_ingredient(ingre->ingredient_name, ingre->amount, ingre->price);
                ingre = ingre->next;
            }
        }
    }
    else {
        printf("\nNo selected order.\n");
    }
}



int add_ingredient(
    struct pizzeria *pizzeria,
    char *ingredient_name,
    int amount,
    double price
) {
    struct order *current = pizzeria->selected;
    if (current == NULL) {
        return INVALID_ORDER;
    }
    
    if (amount <= 0) {
        return INVALID_AMOUNT;
    }
    if (price < 0) {
        return INVALID_PRICE;
    }
     
    pizzeria->selected->add_ingredient = add_ingredient_ordered(ingredient_name, 
    amount, price, pizzeria->selected->add_ingredient);
    return SUCCESS;
}

double calculate_total_profit(struct pizzeria *pizzeria) {

    double capital = 0;
    double profit = 0;
    struct order *customer = pizzeria->selected;
    if (customer == NULL) {
        return INVALID_ORDER;
    }
    
    struct ingredient *current = pizzeria->selected->add_ingredient;
    while (current != NULL) {
        capital += current->amount * current->price;
        current = current->next;
    }
    profit = customer->price - capital;
    return profit;
}

////////////////////////////////////////////////////////////////////////
//                         Stage 3 Functions                          //
////////////////////////////////////////////////////////////////////////


int cancel_order(struct pizzeria *pizzeria) {
    
    struct order *current = pizzeria->orders;
    struct order *selected = pizzeria->selected;
    
    // when no selected order yet
    if (selected == NULL || current == NULL) {
        return INVALID_ORDER;
    }
    // when the selected order is the head of the list.
    if (selected == current) {
    
        free_ingredients_ordered(current->add_ingredient);
        struct order *new_head = current->next;
        free(current);
        pizzeria->orders = new_head;
        pizzeria->selected = new_head;
        return SUCCESS;
    }
    // when the selected order is at other postions.
    while (current->next != selected) {
        current = current->next;
    }
    
    free_ingredients_ordered(current->next->add_ingredient);
    struct order *new_next = current->next->next;
    free(current->next);
    pizzeria->selected = new_next;
    current->next = new_next;
    
    return SUCCESS;
}

void free_pizzeria(struct pizzeria *pizzeria) {
      
    struct order *curr_2 = pizzeria->orders;
    struct ingredient *curr_3 = pizzeria->stock;
    while (curr_2 != NULL) {
        struct order *free_order = curr_2;
        free_ingredients_ordered(curr_2->add_ingredient);
        curr_2 = curr_2->next;
        free(free_order);
    }
    
    while (curr_3 != NULL) {
        struct ingredient *free_stock = curr_3;
        curr_3 = curr_3->next;
        free(free_stock);
    }
    
    free(pizzeria);
    return;
}

int refill_stock(
    struct pizzeria *pizzeria,
    char *ingredient_name,
    int amount,
    double price
) {
    
    if (price < 0) {
        return INVALID_PRICE;
    }
    if (amount <= 0) {
        return INVALID_AMOUNT;
    }
    
    pizzeria->stock = add_ingredient_ordered(ingredient_name, amount, price, 
    pizzeria->stock);
    return SUCCESS;
}

void print_stock(struct pizzeria *pizzeria) {

    struct ingredient *current = pizzeria->stock;
    printf("The stock contains:\n");
    while (current != NULL) {
        print_ingredient(current->ingredient_name, current->amount, 
        current->price);
        current = current->next;
    }
    return;
}

int can_complete_order(struct pizzeria *pizzeria) {
    
    if (pizzeria->selected == NULL) {
        return INVALID_ORDER;
    }
    struct ingredient *current = pizzeria->selected->add_ingredient;
    if (current == NULL) {
        return INVALID_ORDER;
    }
    int checking = 0;
    if (pizzeria->stock == NULL) {
        return INSUFFICIENT_STOCK;
    }
    
    while (current != NULL) {
        checking = ingredient_checking(current, pizzeria->stock);
        if (checking < 2) {
            return INSUFFICIENT_STOCK;
        }
        current = current->next;
    } 
    return SUCCESS;
}

////////////////////////////////////////////////////////////////////////
//                         Stage 4 Functions                          //
////////////////////////////////////////////////////////////////////////

int complete_order(struct pizzeria *pizzeria) {
    int checking = can_complete_order(pizzeria);
    if (checking != SUCCESS) {
        return checking;
    }
    
    else {
        // calculating the amounts of each ingredient in stock after completing
        // the order
        struct ingredient *curr_stock = pizzeria->stock;
        struct ingredient *select = pizzeria->selected->add_ingredient;
        while (curr_stock != NULL && select != NULL) {
            if (strcmp(curr_stock->ingredient_name, select->ingredient_name) == 0) {
                curr_stock->amount -= select->amount;
                curr_stock = curr_stock->next;
                select = select->next;
            }
            else if (strcmp(curr_stock->ingredient_name, select->ingredient_name) != 0) {
                curr_stock = curr_stock->next;
            }
        } 
    }
    
    stock_update(pizzeria);
    // Using 'cancel_order(pizzeria)' function to free the space after finishing
    // the order
    cancel_order(pizzeria);
    return SUCCESS;
}

int save_ingredients(struct pizzeria *pizzeria, char *file_name) {

    return SUCCESS;
}

int load_ingredients(struct pizzeria *pizzeria, char *file_name) {

    return SUCCESS;
}

////////////////////////////////////////////////////////////////////////
//               HELPER FUNCTIONS - Add your own here                 //
////////////////////////////////////////////////////////////////////////

// Prints a single order
//
// `print_order` will be given the parameters:
// - `num` -- the integer that represents which order it is sequentially.
// - `customer` -- the name of the customer for that order.
// - `pizza_name` -- the pizza the customer ordered.
// - `price` -- the price the customer is paying for the pizza.
// - `time_allowed` -- the time the customer will wait for the order.
//
// `print_order` assumes all parameters are valid.
//
// `print_order` returns nothing.
//
// This will be needed for Stage 1.
void print_order(
    int num,
    char *customer,
    char *pizza_name,
    double price,
    int time_allowed
) {

    printf("%02d: %s ordered a %s pizza ($%.2lf) due in %d minutes.\n",
        num, customer, pizza_name, price, time_allowed);

    return;
}

// Prints a single ingredient
//
// `print_ingredient` will be given the parameters:
// - `name` -- the string which contains the ingredient's name.
// - `amount` -- how many of the ingredient we either need or have.
// - `price` -- the price the ingredient costs.
//
// `print_ingredient` assumes all parameters are valid.
//
// `print_ingredient` returns nothing.
//
// This will be needed for Stage 2.
void print_ingredient(char *name, int amount, double price) {
    printf("    %s: %d @ $%.2lf\n", name, amount, price);
}

// This functions is created in order to remove all ingredients in the order 
// that has been freed
void free_ingredients_ordered (struct ingredient *head) {
    while (head != NULL) {
        struct ingredient *temp = head;
        head = head->next;
        free(temp);
    }
    return;
}

// This functions allows to update the stock every time that one order is 
// completed
void stock_update(struct pizzeria *pizzeria) {
    // checking if the amount of ingredient at the head is 0 then remove it
    while (pizzeria->stock != NULL && pizzeria->stock->amount == 0) {
        struct ingredient *temp = pizzeria->stock;
        pizzeria->stock = pizzeria->stock->next;
        free(temp);
    }
    // checking if any other ingredients in stock has 0 amount then remove them
    struct ingredient *current = pizzeria->stock;
    if (current == NULL) {
        return;
    }
    
    while (current->next != NULL) {
        
        if (current->next->amount == 0) {
            struct ingredient *temp = current->next;
            current->next = current->next->next;
            free(temp);
        }
        else {
            current = current->next;
        }
    }
    
    return;
} 

// This function is for checking that if the ingredients in selected order are
// in stock or not  
int ingredient_checking (struct ingredient *a , struct ingredient *head) {
    int checking = 0;
    struct ingredient *current = head;
    while (current != NULL) {
        if (strcmp(a->ingredient_name, current->ingredient_name) == 0) {
            if (a->amount > current->amount) {
                checking = 1;
            } else {
                checking = 2;
            }
        }
        current = current->next;
    }
    return checking;
}

// This function is used for adding new ingredients into the stock or existed 
// orders
struct ingredient *add_ingredient_ordered(char *ingredient_name, int amount, 
double price, struct ingredient *head) {
    int checking = 0;
    
    struct ingredient *new = malloc(sizeof(struct ingredient));
    strcpy(new->ingredient_name, ingredient_name);
    new->amount = amount;
    new->price = price;
    new->next = NULL;
    
    struct ingredient *moving = head;
    // Creating the head of ingredient list
    if (moving == NULL) {
        head = new;
    }
    // New ingredient is alphabetically smaller than the head so return 
    // the new head
    if (moving != NULL && strcmp(moving->ingredient_name, new->ingredient_name) > 0) {
        new->next = head;
        head = new;
        return head;
    }
    // New ingredient is alphabetically equal to the head so increment the head
    // amount
    if (moving != NULL && strcmp(moving->ingredient_name, new->ingredient_name) == 0) {
        moving->amount += new->amount;
        free(new);
        return head;
    }
    // New ingredient already existed in the list
    while (moving != NULL) {
        checking = strcmp(moving->ingredient_name, new->ingredient_name);
        if (checking == 0) {
            moving->amount += new->amount;
            free(new);
            return head;
        }
        moving = moving->next;
    }
    // Return "moving" back to the head of the list after checking
    moving = head;
    
    // New ingredient is alphabetically larger than the head of the list
    if (moving != NULL && strcmp(moving->ingredient_name, new->ingredient_name) < 0) {        
        while (moving->next != NULL && 
        strcmp(moving->ingredient_name, new->ingredient_name) < 0 && 
        strcmp(moving->next->ingredient_name, new->ingredient_name) < 0) {
            moving = moving->next;
        }
        
        new->next = moving->next;
        moving->next = new;
    }   
    return head;
    
}
