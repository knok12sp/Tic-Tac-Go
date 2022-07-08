package main

import "fmt"

type Symbol uint8

const (
	CS = iota
	O
	X
)

type GameStatus uint8

const (
	GameInProgress GameStatus = iota
	GDraw
	FPlayerW
	SPlayerW
)

type iPlayer interface {
	getSymbol() Symbol
	getNextM() (int, int, error)
	getID() int
}

var (
	MoveP1 = [4][2]int{{1, 1}, {2, 0}, {2, 2}, {2, 1}}
	MoveP2 = [4][2]int{{1, 2}, {0, 2}, {0, 0}, {0, 0}}
)

type Player struct {
	symbol Symbol
	index  int
	id     int
}

func (h *Player) getSymbol() Symbol {
	return h.symbol

}
func (h *Player) getNextM() (int, int, error) {
	if h.symbol == CS {
		h.index = h.index + 1
		return MoveP1[h.index-1][0], MoveP1[h.index-1][1], nil

	} else if h.symbol == O {
		h.index = h.index + 1
		return MoveP2[h.index-1][0], MoveP2[h.index-1][1], nil
	}
	return 0, 0, fmt.Errorf("Bad symbol")

}
func (h *Player) getID() int {
	return h.id
}

type BOT struct {
	Symbol Symbol
	id     int
}

func (c *BOT) getSymbol() Symbol {
	return c.Symbol
}
func (c *BOT) getNextM() (int, int, error) {
	return 0, 0, nil

}
func (c *BOT) getID() int {
	return c.id

}

type board struct {
	square    [][]Symbol
	dimension int
}

func (b *board) printBoard() {

	for i := 0; i < b.dimension; i++ {
		for j := 0; j < b.dimension; j++ {
			if b.square[i][j] == X {
				fmt.Print(".")
			} else if b.square[i][j] == CS {
				fmt.Print("X")
			} else {
				fmt.Print("O")
			}
		}
		fmt.Println("")
	}

}
func (b *board) markSymbol(i, j int, symbol Symbol) (bool, Symbol, error) {
	if i > b.dimension || j > b.dimension {
		return false, X, fmt.Errorf("Bigger than the dimension")
	}
	if b.square[i][j] != X {
		return false, X, fmt.Errorf("Already marked")
	}
	if symbol != CS && symbol != O {
		return false, X, fmt.Errorf("False symbol")
	}
	b.square[i][j] = symbol
	win := b.checkW(i, j, symbol)
	return win, symbol, nil

}

func (b *board) checkW(i, j int, symbol Symbol) bool {
	rowMatch := true
	for k := 0; k < b.dimension; k++ {
		if b.square[i][k] != symbol {
			rowMatch = false
		}
	}
	if rowMatch {
		return rowMatch
	}
	columnMatch := true
	for k := 0; k < b.dimension; i++ {
		if b.square[k][j] != symbol {
			columnMatch = false
		}
	}
	if columnMatch {
		return columnMatch
	}

	diagMatch := false
	if i == j {
		diagMatch = true
		for k := 0; k < b.dimension; k++ {
			if b.square[k][k] != symbol {
				diagMatch = false
			}
		}
	}
	return diagMatch
}

type game struct {
	board           *board
	firstPlayer     iPlayer
	secondPlayer    iPlayer
	firstPlayerTurn bool
	moveIndex       int
	gameStatus      GameStatus
}

func initGame(b *board, p1, p2 iPlayer) *game {
	game := &game{
		board:           b,
		firstPlayer:     p1,
		secondPlayer:    p2,
		firstPlayerTurn: true,
		gameStatus:      GameInProgress,
	}
	return game

}
func (g *game) play() error {
	var win bool
	var symbol Symbol
	for {
		if g.firstPlayerTurn {
			x, y, err := g.firstPlayer.getNextM()
			if err != nil {
				return err
			}
			win, symbol, err = g.board.markSymbol(x, y, g.firstPlayer.getSymbol())
			if err != nil {
				return err
			}
			g.firstPlayerTurn = false
			g.printMove(g.firstPlayer, x, y)
		} else {
			x, y, err := g.secondPlayer.getNextM()
			if err != nil {
				return err
			}
			win, symbol, err = g.board.markSymbol(x, y, g.secondPlayer.getSymbol())
			if err != nil {
				return err
			}
			g.firstPlayerTurn = true
			g.printMove(g.secondPlayer, x, y)
		}
		g.moveIndex = g.moveIndex + 1

		g.setGameStatus(win, symbol)
		if g.gameStatus != GameInProgress {
			break
		}
	}
	return nil
}
func (g *game) setGameStatus(win bool, symbol Symbol) {
	if win {
		if g.firstPlayer.getSymbol() == symbol {
			g.gameStatus = FPlayerW
			return
		} else if g.secondPlayer.getSymbol() == symbol {
			g.gameStatus = SPlayerW
			return

		}
	}
	if g.moveIndex == g.board.dimension*g.board.dimension {
		g.gameStatus = GDraw
		return
	}
	g.gameStatus = GameInProgress

}
func (g *game) printMove(player iPlayer, x, y int) {
	symbolString := ""
	symbol := player.getSymbol()
	if symbol == CS {
		symbolString = "X"
	} else if symbol == O {
		symbolString = "o"
	}
	fmt.Printf("Player %d move with simbol %s at posx:%d Y:%d\n", player.getID(), symbolString, x, y)
	g.board.printBoard()
	fmt.Println("")

}

func (g *game) printResult() {
	switch g.gameStatus {
	case GameInProgress:
		fmt.Println("Game in Between")
	case GDraw:
		fmt.Println("game drawn")
	case FPlayerW:
		fmt.Println("First player won")
	case SPlayerW:
		fmt.Println("Second won")
	default:
		fmt.Println("Invalid game status")

	}
	g.board.printBoard()
}

func main() {
	board := &board{
		square:    [][]Symbol{{X, X, X}, {X, X, X}, {X, X, X}},
		dimension: 3,
	}
	player1 := &Player{
		symbol: CS,
		id:     1,
	}
	player2 := &Player{
		symbol: O,
		id:     2,
	}
	game := initGame(board, player1, player2)
	game.play()
	game.printResult()
}
