# javaparta
[![Build Status](https://travis-ci.org/zelfrontial/javaparta.svg?branch=master)](https://travis-ci.org/zelfrontial/javaparta)

617462 asutanahadi Adrian Sutanahadi
613726 dthamrin Denis Thamrin

N = number of Board Piece

Structure of the program

Board encapsulates the state of the game
ZobirstBoard implements extra hashing to store the state of the game
ZobristBoard <- Inherits from Board

First Random player encapsulates all the basic plyae requirement
MinimaxPlayer is a smarter impelemntation
MinimaxPlayer <- Inherits from FirstRandomPlayer

TranspositionTable
ZobristTranspositionTable , Zobristhash, ZobristTableEntry
Transposition table encapsulates the table information and act as memoization

ZobristHash
Stores the random generator and intialize the individual cell hashes.

ZobristTableEntry
Encapsulates what to store in the Table


###############################################################################
# Pseudocode - Loop Checking - Flood Fill Algorithm                           #
###############################################################################
To check whether piece has been captured or not


floodFill(x, y, player)
    if cell is not valid for exploration
        return empty list
    define 4 direction of exploration
    initialise a stack queue
    push cell to the queue
    while queue is not empty                                             O(N)
        pop the queue
        if the current cell is valid for exploration
            record the cell to the list
            for the cell adjacent in 4 direction to the current cell     O(4)
                if the adjacent cell is not valid for exploration
                    return empty
                if it has not been recorded previously
                    push it to the queue
    return the processed cell list
                                                            Worst Case = O(N)

###############################################################################
# Pseudocode - Minimax Player with Alpha-Beta Pruning + Zobrist Hashing       #
###############################################################################


minimax_decision(state, depth)
    alpha = -infinity
    beta = +infinity
    bestScore = -infinity
    bestMove = null


    for each available valid move                                         O(N)
        create a new board with 1 added move
        alpha = max(alpha,                                                 O(N)
                    minimax_value(new state, depth - 1, alpha, beta, false)
        if alpha > bestScore
            record the best move along with the score

minimax_value(state, depth, alpha, beta, maximisingPlayer)
    if depth = 0 or the game is finished
        return the evaluation function of the state
    if there is a better result already available in the zobrist table
        return the value from the zobrist table
    if it is the maximising player
        bestValue = -infinity
        bestMove = null
        for each available move                                            O(N)
            create a new board with 1 added move
            val = minimax_value(new state, depth - 1, alpha, beta, false)
            if val > bestScore
                record the best move along with the score to the zobrist table
            alpha = max(alpha, bestValue)
            if beta <= alpha
                break
        return bestValue
    if it is the minimising player
        bestValue = +infinity
        bestMove = null
        for each available move                                            O(N)
            create a new board with 1 added move
            val = minimax_value(new state, depth - 1, alpha, beta, true)
            if val < bestScore
                record the best move along with the score to the zobrist table
            alpha = min(beta, bestValue)
            if beta <= alpha
                break
        return bestValue
            
MinimaxSearch so O(b^d) b = branching factor d = depth   
                                                             Worst Case = O(b^d)

The minimax worst case time complexity is O(b^d), however we have also
implemented pruning and zobrist hashing, which can dramatically improve
the performance of the search.

Additional note:
The depth increases as the game progresses.
In the early game depth is around 4-6 as in the early game not much decision can be made
Our eval function has encapsulated the early game information so no need to search till the
deepest.

As the game progress it increments the depth up to depth 10

During searching because of the numerous optimization the search can go to Depth 10
in 7x7 Board

In 7x7 Board, If there is 11 move left, it can search up to depth 11 (Perfect play) which
is amazing.




###############################################################################
#Evaluation Function                                                          #
###############################################################################
Advanced Evaluation Function while focusing on preventing getting captured
Score = layerScore + captureScore + clumpScore + side_node;

LayerScore = During Early game, the further from the center, the more chance
to capture someone later in the game. Also can't be captured easily if you are away from the center
During middle part of the game it is considered useless and has weight 0

CaptureScore = The point of the game is to capture, thus giving a very high importance ! *100 weight

ClumpScore = Best Feature we have where we want the player to not clump together in a position
It's strategic if there 2 neighbourhing node (more chance to capture) but
If it's more than 2 it shows clumping which has no value to the game. *10 weight

Side_node = 4 useless edges has bad weight -9001

###############################################################################
#Creative Technique                                                           #
###############################################################################

Move Ordering
Classes: Board.getMove()

Generate all possible moves in current board
With Move ordering where position the leftmost, right most, top and bottom position is added last
As it is considered low value (can't be captured and can't capture anything)
Also prioritize position that is around non empty cell 
It is to simulate a real player where the player will look around the non empty cell and place it
Around there to block or create loop

Transposition Table
Classes: ZobristBoard inherits from Board, ZobristTranspositionTable , Zobristhash, ZobristTable Entry

Holds the hashtable entry
Adatped from Artificial intelligence for games
Transposition Table act as a memoization (dynamic programming) for all the moves that we opened up.
That saying we don't need to open a previously checked node thus saving the time to traverse.
ZobirstBoard implements extra hashing to store the state of the game
It identifies a state by it's hash value which takes O(1). 
Get Entry O(1)
Store Entry O(1)
It just uses TABLE_SIZE amount of memory + entries.
It also uses incremental zobrist hashing so reduce the hashing from o(n) to just O(1) where 
O(n) is the non empty cell in the board

Is implemented as an array of buckets. HashMap is to expensive thus we need to implement
our own hashMap, where it will replace a bucket if the new entry depth is deeper than the previous
entry. 
The key for the bucket is the boardHash. 
To know which bucket it is in hashvalue % bucketsize

ZobristHash
Stores the random generator and intialize the individual cell hashes.

ZobristTableEntry
Encapsulates what to store in the Table
