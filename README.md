import tkinter as tk
from tkinter import messagebox
import os

SCORE_FILE = "scores.txt"

class TicTacToe:
    def __init__(self):
        self.window = tk.Tk()
        self.window.title("Tic Tac Toe")

        self.current_player = "X"
        self.buttons = [[None for _ in range(3)] for _ in range(3)]
        self.create_board()

        if not os.path.exists(SCORE_FILE):
            with open(SCORE_FILE, "w") as f:
                f.write("X Wins: 0\nO Wins: 0\nDraws: 0\n")

        self.window.mainloop()

    def create_board(self):
        for row in range(3):
            for col in range(3):
                btn = tk.Button(
                    self.window, text="", font=("Helvetica", 24), width=5, height=2,
                    command=lambda r=row, c=col: self.make_move(r, c)
                )
                btn.grid(row=row, column=col)
                self.buttons[row][col] = btn

    def make_move(self, row, col):
        btn = self.buttons[row][col]
        if btn["text"] == "":
            btn["text"] = self.current_player
            if self.check_winner():
                messagebox.showinfo("Game Over", f"Player {self.current_player} wins!")
                self.update_score(self.current_player)
                self.reset_board()
            elif self.is_draw():
                messagebox.showinfo("Game Over", "It's a draw!")
                self.update_score("Draw")
                self.reset_board()
            else:
                self.current_player = "O" if self.current_player == "X" else "X"

    def check_winner(self):
        b = self.buttons
        for i in range(3):
            if b[i][0]["text"] == b[i][1]["text"] == b[i][2]["text"] != "":
                return True
            if b[0][i]["text"] == b[1][i]["text"] == b[2][i]["text"] != "":
                return True
        if b[0][0]["text"] == b[1][1]["text"] == b[2][2]["text"] != "":
            return True
        if b[0][2]["text"] == b[1][1]["text"] == b[2][0]["text"] != "":
            return True
        return False

    def is_draw(self):
        return all(btn["text"] != "" for row in self.buttons for btn in row)

    def reset_board(self):
        for row in self.buttons:
            for btn in row:
                btn["text"] = ""
        self.current_player = "X"

    def update_score(self, result):
        with open(SCORE_FILE, "r") as f:
            lines = f.readlines()

        scores = {line.split(":")[0]: int(line.split(":")[1]) for line in lines}

        if result == "X":
            scores["X Wins"] += 1
        elif result == "O":
            scores["O Wins"] += 1
        else:
            scores["Draws"] += 1

        with open(SCORE_FILE, "w") as f:
            for key, value in scores.items():
                f.write(f"{key}: {value}\n")

if __name__ == "__main__":
    TicTacToe()
