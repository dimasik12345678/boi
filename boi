import random


# внедряем исключения
class BoardException(Exception):
    pass


class BoardOutException(BoardException):
    def __str__(self):
        return 'you shot out of the board!'


class AlreadyShotException(BoardException):
    def __str__(self):
        return 'you already shot on this spot'


class ShipPlacementException(BoardException):
    def __str__(self):
        return 'you already place ship on this spot'



class Dot:  # внедряем точки
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __eq__(self, other):
        if not isinstance(other, Dot):
            return NotImplemented
        return self.x == other.x and self.y == other.y


class Ship:  # настройки корабликов
    def __init__(self, bow, length, direction):
        self.bow = bow
        self.length = length
        self.direction = direction
        self.lives = length

    @property
    def dots(self):
        ship_dots = []
        for i in range(self.length):
            cur_x, cur_y = self.bow.x, self.bow.y
            if self.direction == 0:
                cur_x += i

            else:
                cur_y += i
            ship_dots.append(Dot(cur_x, cur_y))
        return ship_dots


class Board:  # создание доски, настройки выстрела и повреждений корабликов
    def __init__(self, hid=False, size=6):
        self.hid = hid
        self.size = size
        self.field = [['O'] * size for a in range(size)]
        self.busy = []
        self.ships = []
        self.count_ships = len(self.ships)


    def add_ship(self, ship):
        for dot in ship.dots:
            if self.out(dot) or dot in self.busy:
                raise ShipPlacementException
        for dot in ship.dots:
            self.field[dot.x][dot.y] = 'ㅁ'
            self.busy.append(dot)
        self.ships.append(ship)
        self.countour(ship)
        self.count_ships = len(self.ships)

    def countour(self, ship, verb=False):
        near_ship = [(-1, -1), (-1, 0), (-1, 1), (0, -1), (0, 1), (1, -1), (1, 0), (1, 1)]
        for dot in ship.dots:
            for d_x, d_y in near_ship:

                cur = Dot(dot.x + d_x, dot.y + d_y)
                if not self.out(cur) and cur not in self.busy:
                    self.busy.append(cur)
                    if verb:
                        self.field[cur.x][cur.y] = '.'

    def out(self, dot):
        return not (0 <= dot.x < self.size and 0 <= dot.y < self.size)

    # # def cross(self, dot, ship):
    #     near_ship = [(-1, -1), (-1, 0), (-1, 1), (0, -1), (0, 1), (1, -1), (1, 0), (1, 1)]
    #     if any(dot in self.busy or dot in near_ship for dot in ship.dots()):
    #         raise ShipCrossException

    def shot(self, dot):
        if self.out(dot):
            raise BoardOutException()
        if dot in self.busy:
            raise AlreadyShotException()
        self.busy.append(dot)
        for ship in self.ships:
            if dot in ship.dots:
                ship.lives -= 1
                self.field[dot.x][dot.y] = 'X'
                if ship.lives == 0:
                    self.countour(ship, verb=True)
                    self.ships.remove(ship)
                    self.count_ships = len(self.ships)
                    print('ship is destroed!!!')
                    return True
                else:
                    print('ship is break')
                    return True
        self.field[dot.x][dot.y] = 'T'
        print('miss')
        return False

    def __str__(self):
        res = '|' + '|'.join(str(i + 1) for i in range(self.size)) + '|\n'
        for i, row in enumerate(self.field):
            res += f'{i + 1} |' + '|'.join(row) + '|\n'
        if self.hid:
            res = res.replace('ㅁ', 'O')
        return res



    def clear_busy_point(self):
        self.busy = []


class Player:  # родительский класс
    def __init__(self, board, board_enemy):
        self.board = board
        self.board_enemy = board_enemy

    def ask(self):
        raise NotImplementedError

    def move(self):
        while True:
            try:
                target = self.ask()
                again = self.board_enemy.shot(target)
                return again
            except BoardException as e:
                print(e)


class User(Player):  # игрок человек
    def ask(self):
        while True:
            try:
                coordinats = input('enter your coord by x, y: ').split()
                if len(coordinats) != 2:  # or not all(coordinats.isdigit() for coord in coordinats):
                    raise ValueError('not correct format')
                x, y = map(int, coordinats)
                if not (1 <= x <= self.board.size and 1 <= y <= self.board.size):
                    raise ValueError('out from play board')
                return Dot(x - 1, y - 1)
            except ValueError as e:
                print(e)


class AI(Player):  # игрок ии
    def ask(self):
        x = random.randint(0, self.board.size - 1)
        y = random.randint(0, self.board.size - 1)
        print(f'AI move {x + 1}, {y + 1}')
        return Dot(x, y)


class Game:  # соединяем вск в мегазорда
    def __init__(self, size=6):
        self.size = size
        self.user_board = self.random_board()
        self.ai_board = self.random_board(True)
        self.ai = AI(self.ai_board, self.user_board)
        self.user = User(self.user_board, self.ai_board)

    def random_board(self, hid=False):
        board = Board(hid=hid, size=self.size)
        ship_lives = [3, 2, 2, 1, 1, 1, 1]
        attemps = 0
        for lives in ship_lives:
            placed = False
            while not placed and attemps < 2000:
                ship = Ship(Dot(random.randint(0, self.size - 1), random.randint(0, self.size - 1)), lives,
                            random.randint(0, 1))

                try:
                    board.add_ship(ship)
                    placed = True
                except ShipPlacementException:
                    attemps += 1
                if not placed:
                    print('cant to place all ships, please do it again')
                    continue

        board.clear_busy_point()
        return board


    def greet(self):
        print('welcome to the battle ships')
        print('x is stroke, y is pillar')

    def loop(self):
        num = 0
        while True:
            print('user board: ')
            print(self.user.board)
            print('\nai board: ')
            print(self.ai.board)
            if num % 2 == 0:
                print('\nusers move:')
                repeat = self.user.move()
            else:
                print('\nai move: ')
                repeat = self.ai.move()
            if not repeat:
                num += 1

            if self.ai.board.count_ships == 0:
                print('user win')
                break
            if self.user.board.count_ships == 0:
                print('ai wins')
                break

    def start(self):
        self.greet()
        self.loop()


if __name__ == '__main__':
    g = Game()
    g.start()
