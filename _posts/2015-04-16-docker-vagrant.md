---
layout: post
title: "Docker with Vagrant"
description: ""
category: "tools"
---

After having dealt with enough Production deployment issues, I started looking at docker which is a lightweight container to run applications in. 
Being familiar with Vagrant, I decided to see how the two tie in together.
For the unfamiliar:

Vagrant is an automation tool with a domain-specific language (DSL) that is used 
to automate the creation of VMs and VM environments.
Using Vagrant's DSL, we can define a base set of instructions to create a VM .
This allows for automated creation of VMs based on said script. 
This helps in creation of consistent environments across all regions(Dev, QA, Production).

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


	
