---
layout: post
title: "N Queens"
description: ""
category: "algorithms"
---
So Today I thought I would try to tackle an age old algorithms problem I had been asked to 
solve in a couple of interviews in the past.. the nQueens problem. For the unfamiliar, the problem
is to find a way to arrange n Queens on a n*n chess board in a manner that all are safe from each other. 
Took me a bit longer than I thought it would, but below is my solution in Ruby:

>
 require 'set'

class EightQueen

  def initialize()
  end

 def markQueens

    ytaken=SortedSet.new([0,1,2,3,4,5,6,7])
    board=Hash.new(0)
    puts getQueens(0, ytaken, board).inspect
 end

 def getQueens(x, ytaken, board)

    invalidY = Set.new
    validY = nextValidY(x, ytaken, board, invalidY)
    if(validY.nil?)
      return nil
    end
    board[[x,validY]]=1
    ytaken.delete(validY)
    if(x!=7)
      puts ytaken.inspect
      nextX=x+1
      subBoard = getQueens(nextX, ytaken, board)

      while(subBoard.nil?) do
        board.delete([x,validY])
        ytaken.add(validY)
        invalidY.add(validY)

        validY = nextValidY(x, ytaken, board, invalidY)
        if(validY.nil?)
          return nil
        end
        ytaken.delete(validY)
        invalidY.delete(validY)
        board[[x,validY]]=1
        subBoard = getQueens(nextX, ytaken, board)
      end
      board.merge(subBoard)
    end
      return board


 end

def nextValidY(x, ytaken, board, invalidY)

    ytaken.each {|yItr|
         if(!(invalidY.include?(yItr)) && isValid(x, yItr, board))
             return yItr
          end

        }
      return nil
end

 def isValid(x, y, board)
   board.each do |key, array|
      if((key[0]-x).abs == (key[1]-y).abs)
        return false
      end
    end
    return true
 end
end



c = EightQueen.new
c.markQueens
