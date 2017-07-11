
$(document).ready(function() {
  game.init();
});


// Objects

var board = {
  board: [],
  init: function() {
    this.board = [];
    this.createBoard();
    this.renderBoard();
  },
  createBoard: function() {
    for (var i = 0; i < 3; i++) {
      this.board.push([" ", " ", " "]);
    }
  },
  renderBoard: function() {
    $(".board-container").append("<table class='board'></table>");
    for (var i = 0; i < this.board.length; i++) {
      $(".board").append("<tr class='board-row-" + i + "'></tr>");
      for (var j = 0; j < this.board.length; j++) {
        $(".board-row-" + i).append("<td class='board-space' data-row='" + i + 
                                    "' data-col='" + j + "'>" + 
                                    this.board[i][j] + "</td>")
      }
    }
  },
  addSymbol: function(space, symbol) {
    this.board[space.data("row")][space.data("col")] = symbol;
    space.text(symbol);
  },
}

var game = {
  currentPlayer: "X",
  gameover: false,
  init: function() {
    $(".board-container").empty();
    $(".gameover-container").empty();
    this.addTurnText();
    this.currentPlayer = "X";
    this.gameover = false;
    board.init();
    this.takeTurn();
  },
  addTurnText: function() {
    $(".board-container").append("<div class='turn-text'>" + 
                                 this.currentPlayer + ", it's your turn.</div>");
  },
  takeTurn: function() {
    $(".board-space").on("click", function() {
      if (!game.gameover && game.validMove($(this))) {
        board.addSymbol($(this), game.currentPlayer);
        console.log(board.board);
        if (game.win(game.currentPlayer)) {
          game.winScreen(game.currentPlayer);
        } else if (game.tie()) {
          game.tieScreen();
        } else {
          game.currentPlayer = game.currentPlayer === "X" ? "O" : "X";
          $(".turn-text").text(game.currentPlayer + ", it's your turn.");
        }
      }
    });
  },
  validMove: function(space) {
    return board.board[space.data("row")][space.data("col")] === " ";
  },
  win: function(symbol) {
    var gameboard = board.board
    for (var i = 0; i < gameboard.length; i++) {
      if ((gameboard[i][0] === symbol &&
           gameboard[i][1] === symbol &&
           gameboard[i][2] === symbol) ||
          (gameboard[0][i] === symbol &&
           gameboard[1][i] === symbol &&
           gameboard[2][i] === symbol)) {
        return true;
      }
    }
    if ((gameboard[0][0] === symbol &&
         gameboard[1][1] === symbol &&
         gameboard[2][2] === symbol) ||
        (gameboard[2][0] === symbol &&
         gameboard[1][1] === symbol &&
         gameboard[0][2] === symbol)) {
      return true;
    }
    return false;
  },
  tie: function() {
    for (var i = 0; i < board.board.length; i++) {
      for (var j = 0; j < board.board.length; j++) {
        if (board.board[i][j] === " ") {
          return false;
        }
      }
    }
    return true;
  },
  winScreen: function(symbol) {
    this.gameover = true;
    $(".turn-text").text(symbol + ", you won!");
    this.playAgain();
  },
  tieScreen: function() {
    this.gameover = true;
    $(".turn-text").text("It's a tie!");
    this.playAgain();
  },
  playAgain: function() {
    $(".gameover-container").append("<div class='playagain'><button class='playagain-btn'>Play again?</button></div>");
    $(".playagain-btn").on("click", game.init.bind(game));
  },
}