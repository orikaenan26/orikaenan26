
ori
26
2import pygame
import random

pygame.init()

# הגדרות בסיסיות
SCREEN_WIDTH, SCREEN_HEIGHT = 300, 600
BLOCK_SIZE = 30
GAME_WIDTH, GAME_HEIGHT = SCREEN_WIDTH // BLOCK_SIZE, SCREEN_HEIGHT // BLOCK_SIZE

screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Tetris")

# צבעים
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
GREEN = (0, 255, 0)
BLUE = (0, 0, 255)
CYAN = (0, 255, 255)
YELLOW = (255, 255, 0)
MAGENTA = (255, 0, 255)
ORANGE = (255, 165, 0)

# צורות
SHAPES = [
    [[1, 1, 1, 1]],
    [[1, 1], [1, 1]],
    [[0, 1, 0], [1, 1, 1]],
    [[1, 1, 0], [0, 1, 1]],
    [[0, 1, 1], [1, 1, 0]],
    [[1, 1, 1], [1, 0, 0]],
    [[1, 1, 1], [0, 0, 1]]
]
SHAPE_COLORS = [CYAN, YELLOW, PURPLE, GREEN, RED, BLUE, ORANGE]

# מחלקה לבלוקים
class Tetromino:
    def __init__(self):
        self.shape = random.choice(SHAPES)
        self.color = SHAPE_COLORS[SHAPES.index(self.shape)]
        self.x = GAME_WIDTH // 2 - len(self.shape[0]) // 2
        self.y = 0

    def rotate(self):
        self.shape = [list(row) for row in zip(*self.shape[::-1])]

def create_grid(locked_positions={}):
    grid = [[BLACK for _ in range(GAME_WIDTH)] for _ in range(GAME_HEIGHT)]
    for y, row in enumerate(grid):
        for x, color in enumerate(row):
            if (x, y) in locked_positions:
                grid[y][x] = locked_positions[(x, y)]
    return grid

def draw_grid(grid):
    for y in range(GAME_HEIGHT):
        for x in range(GAME_WIDTH):
            pygame.draw.rect(screen, grid[y][x], (x * BLOCK_SIZE, y * BLOCK_SIZE, BLOCK_SIZE, BLOCK_SIZE), 0)
    for x in range(GAME_WIDTH):
        pygame.draw.line(screen, WHITE, (x * BLOCK_SIZE, 0), (x * BLOCK_SIZE, SCREEN_HEIGHT))
    for y in range(GAME_HEIGHT):
        pygame.draw.line(screen, WHITE, (0, y * BLOCK_SIZE), (SCREEN_WIDTH, y * BLOCK_SIZE))

def valid_space(tetromino, grid):
    for y, row in enumerate(tetromino.shape):
        for x, cell in enumerate(row):
            if cell:
                if x + tetromino.x < 0 or x + tetromino.x >= GAME_WIDTH or y + tetromino.y >= GAME_HEIGHT:
                    return False
                elif grid[y + tetromino.y][x + tetromino.x] != BLACK:
                    return False
    return True

def clear_rows(grid, locked):
    cleared = 0
    for y in range(GAME_HEIGHT-1, -1, -1):
        if BLACK not in grid[y]:
            cleared += 1
            del grid[y]
            grid.insert(0, [BLACK for _ in range(GAME_WIDTH)])
    return cleared

def draw_next_shape(tetromino):
    font = pygame.font.SysFont('Arial', 30)
    label = font.render('Next Shape:', 1, WHITE)
    screen.blit(label, (SCREEN_WIDTH + 10, 10))

    for y, row in enumerate(tetromino.shape):
        for x, cell in enumerate(row):
            if cell:
                pygame.draw.rect(screen, tetromino.color, (SCREEN_WIDTH + 10 + x * BLOCK_SIZE, 50 + y * BLOCK_SIZE, BLOCK_SIZE, BLOCK_SIZE), 0)

def main():
    grid = create_grid()
    locked_positions = {}
    change_piece = False
    current_piece = Tetromino()
    next_piece = Tetromino()
    clock = pygame.time.Clock()
    fall_time = 0
    score = 0

    running = True
    while running:
        grid = create_grid(locked_positions)
        fall_speed = 0.27

        fall_time += clock.get_rawtime()
        clock.tick()

        if fall_time / 1000 >= fall_speed:
            fall_time = 0
            current_piece.y += 1
            if not valid_space(current_piece, grid) and current_piece.y > 0:
                current_piece.y -= 1
                change_piece = True

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
                pygame.quit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    current_piece.x -= 1
                    if not valid_space(current_piece, grid):
                        current_piece.x += 1
                elif event.key == pygame.K_RIGHT:
                    current_piece.x += 1
                    if not valid_space(current_piece, grid):
                        current_piece.x -= 1
                elif event.key == pygame.K_DOWN:
                    current_piece.y += 1
                    if not valid_space(current_piece, grid):
                        current_piece.y -= 1
                elif event.key == pygame.K_UP:
                    current_piece.rotate()
                    if not valid_space(current_piece, grid):
                        current_piece.rotate()
                        current_piece.rotate()
                        current_piece.rotate()

        shape_pos = [(current_piece.x + x, current_piece.y + y) for y, row in enumerate(current_piece.shape) for x, cell in enumerate(row) if cell]
        for x, y in shape_pos:
            if y > -1:
                grid[y][x] = current_piece.color

        if change_piece:
            for x, y in shape_pos:
                locked_positions[(x, y)] = current_piece.color
            current_piece = next_piece
            next_piece = Tetromino()
            change_piece = False
            score += clear_rows(grid, locked_positions) * 10

        screen.fill(BLACK)
        draw_grid(grid)
        draw_next_shape(next_piece)
        pygame.display.update()

main()
6
26
26
26
26
26
26
26
26
- 👋 Hi, I’m @orikaenan26
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
orikaenan26/orikaenan26 is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
