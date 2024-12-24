# futureintern_c-_task04
#include <iostream>
#include <conio.h>
#include <windows.h>
#include <vector>
using namespace std;

bool gameOver;
const int width = 20;
const int height = 20;
int x, y, foodX, foodY, score;
vector<pair<int, int>> snake; // To store snake segments
enum eDirection { STOP = 0, LEFT, RIGHT, UP, DOWN };
eDirection dir;

void setup() {
    gameOver = false;
    dir = STOP;
    x = width / 2;
    y = height / 2;
    foodX = rand() % width;
    foodY = rand() % height;
    score = 0;
    snake.clear();
    snake.push_back({x, y});
}

void draw() {
    system("cls"); // Clear the screen

    // Draw the boundary
    for (int i = 0; i < width + 2; i++)
        cout << "#";
    cout << endl;

    // Draw the game board
    for (int i = 0; i < height; i++) {
        for (int j = 0; j < width; j++) {
            if (j == 0) cout << "#"; // Left boundary
            if (i == y && j == x)
                cout << "O"; // Snake head
            else if (i == foodY && j == foodX)
                cout << "F"; // Food
            else {
                bool isBody = false;
                for (size_t k = 1; k < snake.size(); k++) {
                    if (snake[k].first == j && snake[k].second == i) {
                        cout << "o"; // Snake body
                        isBody = true;
                        break;
                    }
                }
                if (!isBody)
                    cout << " ";
            }
            if (j == width - 1) cout << "#"; // Right boundary
        }
        cout << endl;
    }

    // Bottom boundary
    for (int i = 0; i < width + 2; i++)
        cout << "#";
    cout << endl;

    cout << "Score: " << score << endl;
}

void input() {
    if (_kbhit()) { // Check if a key is pressed
        switch (_getch()) {
            case 'a': dir = LEFT; break;
            case 'd': dir = RIGHT; break;
            case 'w': dir = UP; break;
            case 's': dir = DOWN; break;
            case 'x': gameOver = true; break; // Quit the game
        }
    }
}

void logic() {
    // Move the snake
    int prevX = x;
    int prevY = y;
    switch (dir) {
        case LEFT: x--; break;
        case RIGHT: x++; break;
        case UP: y--; break;
        case DOWN: y++; break;
        default: break;
    }

    // Check if snake collides with the boundaries
    if (x < 0 || x >= width || y < 0 || y >= height) {
        gameOver = true;
        return;
    }

    // Update snake position
    snake.insert(snake.begin(), {x, y});
    if (x == foodX && y == foodY) {
        score++;
        foodX = rand() % width;
        foodY = rand() % height;
    } else {
        snake.pop_back(); // Remove tail segment if no food eaten
    }

    // Check if snake collides with itself
    for (size_t i = 1; i < snake.size(); i++) {
        if (snake[i].first == x && snake[i].second == y) {
            gameOver = true;
            return;
        }
    }
}

int main() {
    setup();
    while (!gameOver) {
        draw();
        input();
        logic();
        Sleep(100); // Control the speed of the game
    }
    cout << "Game Over! Your final score is: " << score << endl;
    return 0;
}
