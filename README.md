# FinalDefenderExampleCode
This is the code that runs the main game scene of Final Defender: The Last Stand

//
//  GameScene.swift
//  Last Defender
//
//  Created by Nicolas Mewhirter on 5/30/19.
//  Copyright © 2019 42archerStudios. All rights reserved.
//

import SpriteKit
import GameplayKit

class GameScene: SKScene {
    
    //////////VARIABLE PREP
    var background:SKSpriteNode!
    var screenCover:SKSpriteNode!
    
    var leftButtonNode:SKSpriteNode!
    var rightButtonNode:SKSpriteNode!
    var fireButtonNode:SKSpriteNode!
    var pauseButtonNode:SKSpriteNode!
    var menuButtonNode:SKSpriteNode!
    var resumeButtonNode:SKSpriteNode!
    
    var hitEnd1:SKSpriteNode!
    var hitEnd2:SKSpriteNode!
    var hitEnd3:SKSpriteNode!
    var hitEnd4:SKSpriteNode!
    
    var life3Node:SKSpriteNode!
    var life2Node:SKSpriteNode!
    var life1Node:SKSpriteNode!
    
    var fighter:SKSpriteNode!
    var lives = 3
    var orientation = 0
    
    var numOfEnemies = 0
    var score = 0
    var scoreLabelNode:SKLabelNode!
    var incrementIndicatorNode:SKLabelNode!
    
    var laserHitCounter = 0
    var enemiesKilled = 0
    var shotsFired = 0
    var shotsHit = 0
    
    var gamePaused = false
    
    let ninetyDeg = CGFloat.pi / 2
    let oneEightyDeg = CGFloat.pi
    let twoSeventyDeg = 3 * CGFloat.pi / 2
    //////////
    
    override func didMove(to view: SKView) {
        //////////PAIRING OF VARIABLES
        background = (self.childNode(withName: "Background") as! SKSpriteNode)
        screenCover = (self.childNode(withName: "ScreenCover") as! SKSpriteNode)
        
        leftButtonNode = (self.childNode(withName: "LeftButton") as! SKSpriteNode)
        rightButtonNode = (self.childNode(withName: "RightButton") as! SKSpriteNode)
        fireButtonNode = (self.childNode(withName: "FireButton") as! SKSpriteNode)
        pauseButtonNode = (self.childNode(withName: "PauseButton") as! SKSpriteNode)
        menuButtonNode = (self.childNode(withName: "MainMenuButton") as! SKSpriteNode)
        resumeButtonNode = (self.childNode(withName: "ResumeButton") as! SKSpriteNode)
        
        hitEnd1 = (self.childNode(withName: "hitEnd1") as! SKSpriteNode)
        hitEnd2 = (self.childNode(withName: "hitEnd2") as! SKSpriteNode)
        hitEnd3 = (self.childNode(withName: "hitEnd3") as! SKSpriteNode)
        hitEnd4 = (self.childNode(withName: "hitEnd4") as! SKSpriteNode)
        
        life3Node = (self.childNode(withName: "Life3") as! SKSpriteNode)
        life2Node = (self.childNode(withName: "Life2") as! SKSpriteNode)
        life1Node = (self.childNode(withName: "Life1") as! SKSpriteNode)
        
        fighter = (self.childNode(withName: "Fighter") as! SKSpriteNode)
        
        scoreLabelNode = (self.childNode(withName: "ScoreLabel") as! SKLabelNode)
        scoreLabelNode.text = "0"
        incrementIndicatorNode = (self.childNode(withName: "IncrementIndicator") as! SKLabelNode)
        //////////
    }
    
    override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
        let touch:UITouch = touches.first! //set a touch to first touch?
        let positionInScene = touch.location(in: self) //location of touch
        let touchedNode = self.atPoint(positionInScene) //creates a variable to store the location of the touch and any possible nodes
        let rotateLeft = SKAction.rotate(byAngle: ninetyDeg, duration: 0.15)
        let rotateRight = SKAction.rotate(byAngle: -1 * ninetyDeg, duration: 0.15)
        
        //let defaults = UserDefaults.standard
        
        if let name = touchedNode.name { //if there is a node at the touch location, get name
            if name == "LeftButton" { //check if name matches
                leftButtonNode.texture = SKTexture(imageNamed: "ButtonLeft_Pressed")
                fighter.run(rotateLeft) //run left rotate action
                orientation = orientation + 1 //update orientation
                if orientation > 3 { //closes circular loop
                    orientation = 0
                }
            }
            if name == "RightButton" { //check if name matches
                rightButtonNode.texture = SKTexture(imageNamed: "ButtonRight_Pressed")
                fighter.run(rotateRight) //run right rotate action
                orientation = orientation - 1 //updte orientation
                if orientation < 0 { //closes circular loop
                    orientation = 3
                }
            }
            if name == "FireButton" { //check if name matches
                fireButtonNode.texture = SKTexture(imageNamed: "FireButton_Pressed")
                fireLaser()
            }
            if name == "PauseButton" {
                /* SOUND CODE FOR IMPLEMENTATION ONCE SOUNDS CREATED!
                if (defaults.bool(forKey: defaultsKeys.soundOff)) == false {
                    //play button sound!
                }
                */
                pauseButtonNode.texture = SKTexture(imageNamed: "PauseButton_Pressed")
                screenCover.zPosition = 5
                menuButtonNode.zPosition = 6
                resumeButtonNode.zPosition = 6
                gamePaused = true
            }
            if name == "ResumeButton" {
                scene?.view?.isPaused = false
                /* SOUND CODE FOR IMPLEMENTATION ONCE SOUNDS CREATED!
                if (defaults.bool(forKey: defaultsKeys.soundOff)) == false {
                    //play button sound!
                }
                */
                resumeButtonNode.texture = SKTexture(imageNamed: "ResumeGameButton_Pressed")
                screenCover.zPosition = -1
                menuButtonNode.zPosition = -1
                resumeButtonNode.zPosition = -1
                gamePaused = false
            }
            if name == "MainMenuButton" { //check if name matches
                scene?.view?.isPaused = false
                gamePaused = false
                /* SOUND CODE FOR IMPLEMENTATION ONCE SOUNDS CREATED!
                if (defaults.bool(forKey: defaultsKeys.soundOff)) == false {
                    //play button sound!
                }
                */
                menuButtonNode.texture = SKTexture(imageNamed: "MenuButton_Pressed") //update button
                let reveal = SKTransition.reveal(with: .up, duration: 1)
                let scene = MainMenuScene(fileNamed: "MainMenuScene")! //transition to MainMenuScene
                scene.scaleMode = .aspectFit
                self.view?.presentScene(scene, transition: reveal)
            }
        }
    }
    
    override func touchesEnded(_ touches: Set<UITouch>, with event: UIEvent?) {
        //replace textures of all buttons
        leftButtonNode.texture = SKTexture(imageNamed: "ButtonLeft")
        rightButtonNode.texture = SKTexture(imageNamed: "ButtonRight")
        fireButtonNode.texture = SKTexture(imageNamed: "FireButton")
        pauseButtonNode.texture = SKTexture(imageNamed: "PauseButton")
        resumeButtonNode.texture = SKTexture(imageNamed: "ResumeGameButton")
        menuButtonNode.texture = SKTexture(imageNamed: "MenuButton")
    }
    
    func fireLaser() {
        /////Initialize Laser
        let laserNode = SKSpriteNode(imageNamed: "LaserBolt")
        laserNode.name = "laser"
        laserNode.position = fighter.position
        laserNode.zPosition = 3
        self.addChild(laserNode)
        /////
        
        //let defaults = UserDefaults.standard
        
        /////Variable Setup
        let posX = frame.width
        let negX = -(frame.width)
        let posY = frame.height
        let negY = -(frame.height)
        
        let animationDuration:TimeInterval = 0.5
        var actionArray = [SKAction]()
        /////
        
        /* In the if statements below, the orientation is used to assimilate the laser orientation to match the ship*/
        if (orientation == 3) {
            laserNode.zRotation = twoSeventyDeg
            actionArray.append(SKAction.move(to: CGPoint(x: posX, y: 0), duration: animationDuration))
            actionArray.append(SKAction.removeFromParent())
        }
        else if (orientation == 1) {
            laserNode.zRotation = ninetyDeg
            actionArray.append(SKAction.move(to: CGPoint(x: negX, y: 0), duration: animationDuration))
            actionArray.append(SKAction.removeFromParent())
        }
        else if (orientation == 0) {
            laserNode.zRotation = 0
            actionArray.append(SKAction.move(to: CGPoint(x: 0, y: posY), duration: animationDuration))
            actionArray.append(SKAction.removeFromParent())
        }
        else if (orientation == 2) {
            laserNode.zRotation = oneEightyDeg
            actionArray.append(SKAction.move(to: CGPoint(x: 0, y: negY), duration: animationDuration))
            actionArray.append(SKAction.removeFromParent())
        }
        /* SOUND CODE FOR IMPLEMENTATION ONCE SOUNDS CREATED!
        if (defaults.bool(forKey: defaultsKeys.soundOff)) == false {
            //play laser sound!
        }
        */
        laserNode.run(SKAction.sequence(actionArray)) //run the laser action sequence
        shotsFired = shotsFired + 1 //update...
    }
    
    func spawnVirus() {
        //GENERAL VARIABLE SETUP
        var random = 0
        var rareRandom = 0
        var spawnNew = false
        var pauseNeeded = false
        let pauseInterval:TimeInterval = 2
        //
        
        //RARE SPIKESHIP FREQUENCY RATES
        let oneInTwenty = 5
        let oneInTen = 10
        let oneInEight = 15
        let oneInFive = 20
        //
        
        //SPIKESHIP SPEEDS
        let sevenSec:TimeInterval = 7
        let sixSec:TimeInterval = 6
        let fiveSec:TimeInterval = 5
        let fourSec:TimeInterval = 4
        let threeSec:TimeInterval = 3
        let twoHalfSec:TimeInterval = 2.5
        //
        
        //SPIKESHIP DENSITIES
        let twoSpike = 2
        let threeSpike = 3
        let fourSpike = 4
        let fiveSpike = 5
        let sixSpike = 6
        let sevenSpike = 7
        //
        
        //CURRENT VARIABLES
        var currentSec:TimeInterval!
        var currentDen = 0
        var currentFreq = 0
        //
        
        //Switch case used to increase difficulty of game according to score. Factors affected are enemy density, enemy speed, and rare enemy frequency
        switch enemiesKilled {
        case 0...9:
            currentSec = sevenSec
            currentDen = twoSpike
            currentFreq = oneInTwenty
        case 10...24:
            currentSec = sixSec
            currentDen = twoSpike
            currentFreq = oneInTwenty
        case 25...49:
            currentSec = sixSec
            currentDen = threeSpike
            currentFreq = oneInTen
        case 50...74:
            currentSec = fiveSec
            currentDen = threeSpike
            currentFreq = oneInTen
        case 75...99:
            currentSec = fiveSec
            currentDen = fourSpike
            currentFreq = oneInTen
        case 100...149:
            currentSec = fourSec
            currentDen = fourSpike
            currentFreq = oneInEight
        case 150...199:
            currentSec = fourSec
            currentDen = fiveSpike
            currentFreq = oneInEight
        case 200...274:
            currentSec = fourSec
            currentDen = fiveSpike
            currentFreq = oneInEight
        case 275...349:
            currentSec = threeSec
            currentDen = fiveSpike
            currentFreq = oneInFive
        case 350...424:
            currentSec = threeSec
            currentDen = sixSpike
            currentFreq = oneInFive
        case 425...499:
            currentSec = twoHalfSec
            currentDen = sixSpike
            currentFreq = oneInFive
        case _ where enemiesKilled >= 500:
            currentSec = twoHalfSec
            currentDen = sevenSpike
            currentFreq = oneInFive
        default:
            print("Something went wrong! :(")
        }
        
        var actionArray = [SKAction]() //list to hold queued actions
        
        if numOfEnemies < currentDen { //compares number of viruses on screen with how many there should be...
            spawnNew = true //if there are less viruses on screen, prepare to spawn a new one
            random = Int.random(in: 0 ..< 4) //generate a random int between 0 and 3 inclusive
        }
        
        if spawnNew { //if deemed necessary to spawn new...
            rareRandom = Int.random(in: 0 ..< 100) //generate random between 0 and 99 inclusive
            
            let enemyNode = SKSpriteNode(imageNamed: "CommonEnemy") //default enemy specs
            enemyNode.name = "CommonSpikeship"
            enemyNode.zPosition = 3
            enemyNode.setScale(0.6)
            
            if (rareRandom < currentFreq) { //if generated number is less than frequency...
                enemyNode.texture = SKTexture(imageNamed: "RareEnemy") //change specs to rare enemy
                enemyNode.name = "RareSpikeship"
            }
            
            numOfEnemies = numOfEnemies + 1 //update...
            
            //ENEMY SPAWN POSITIONS
            let posX = frame.width / 2 + 50
            let negX = -(frame.width / 2 + 50)
            let posY = frame.height / 2 + 50
            let negY = -(frame.height / 2 + 50)
            //
            
            /*Based on randomly generated number, new enemy is spawned in lane. Spawnpoint is checked for overlap, and if one is detected, a pauseneeded variable is updated*/
            switch random {
            case 0:
                enemyNode.position = CGPoint(x: 0, y: posY)
                self.addChild(enemyNode)
                enumerateChildNodes(withName: "CommonSpikeship") {node, _ in
                    let enemy = node as! SKSpriteNode
                    if enemyNode != enemy && enemyNode.frame.intersects(enemy.frame) {
                        pauseNeeded = true
                    }
                }
                enumerateChildNodes(withName: "RareSpikeship") {node, _ in
                    let enemy = node as! SKSpriteNode
                    if enemyNode != enemy && enemyNode.frame.intersects(enemy.frame) {
                        pauseNeeded = true
                    }
                }
                
            case 1:
                enemyNode.position = CGPoint(x: negX, y: 0)
                self.addChild(enemyNode)
                enumerateChildNodes(withName: "CommonSpikeship") {node, _ in
                    let enemy = node as! SKSpriteNode
                    if enemyNode != enemy && enemyNode.frame.intersects(enemy.frame) {
                        pauseNeeded = true
                    }
                }
                enumerateChildNodes(withName: "RareSpikeship") {node, _ in
                    let enemy = node as! SKSpriteNode
                    if enemyNode != enemy && enemyNode.frame.intersects(enemy.frame) {
                        pauseNeeded = true
                    }
                }
                
            case 2:
                enemyNode.position = CGPoint(x: 0, y: negY)
                self.addChild(enemyNode)
                enumerateChildNodes(withName: "CommonSpikeship") {node, _ in
                    let enemy = node as! SKSpriteNode
                    if enemyNode != enemy && enemyNode.frame.intersects(enemy.frame) {
                        pauseNeeded = true
                    }
                }
                enumerateChildNodes(withName: "RareSpikeship") {node, _ in
                    let enemy = node as! SKSpriteNode
                    if enemyNode != enemy && enemyNode.frame.intersects(enemy.frame) {
                        pauseNeeded = true
                    }
                }
    
            case 3:
                enemyNode.position = CGPoint(x: posX, y: 0)
                self.addChild(enemyNode)
                enumerateChildNodes(withName: "CommonSpikeship") {node, _ in
                    let enemy = node as! SKSpriteNode
                    if enemyNode != enemy && enemyNode.frame.intersects(enemy.frame) {
                        pauseNeeded = true
                    }
                }
                enumerateChildNodes(withName: "RareSpikeship") {node, _ in
                    let enemy = node as! SKSpriteNode
                    if enemyNode != enemy && enemyNode.frame.intersects(enemy.frame) {
                        pauseNeeded = true
                    }
                }
                
            default:
                print("Something went wrong :(")
            }
            
            if pauseNeeded { //if a pause was determined to be need above...
                actionArray.append(SKAction.wait(forDuration: pauseInterval)) //wait on the enemy movement action
            }
            
            actionArray.append(SKAction.move(to: CGPoint(x: 0, y: 0), duration: currentSec)) //add action to array
            actionArray.append(SKAction.removeFromParent())
            enemyNode.run(SKAction.sequence(actionArray)) //run action
        }
    }
    
    func checkCollisions() {
        var laserHitEnemy: [SKSpriteNode] = [] //list of hit enemies
        var lasersLanded: [SKSpriteNode] = [] //list of lasers that landed a hit
        var enemyHitFighter: [SKSpriteNode] = [] //list of enemies that hit fighter
        
        let hitbox = self.fighter.frame.insetBy(dx: 10, dy: 10) //hitbox to represent fighter
        
        //let defaults = UserDefaults.standard
        
        /////////BELOW LOOP IS FOR COMMON SPIKESHIPS
        enumerateChildNodes(withName: "CommonSpikeship") {node, _ in //move through all commons
            let enemy = node as! SKSpriteNode //alias to add to list
            self.enumerateChildNodes(withName: "laser") {node, _ in //move through all lasers
                let laser = node as! SKSpriteNode //alias to add to list
                if enemy.frame.intersects(laser.frame) { //if laser intersects enemy...
                    laserHitEnemy.append(enemy) //add enemy to hit list
                    lasersLanded.append(laser) //add laser to landed list
                }
                if  self.hitEnd1.frame.intersects(laser.frame) ||  self.hitEnd2.frame.intersects(laser.frame) || self.hitEnd3.frame.intersects(laser.frame) || self.hitEnd4.frame.intersects(laser.frame) { //if laser makes it to end of lane...
                    
                    laser.removeFromParent()
                    self.laserHitCounter = 0 //...reset hit counter
                    self.incrementIndicatorNode.text = "+5"
                }
            }
            if enemy.frame.intersects(hitbox) { //if enemy intersects fighter...
                enemyHitFighter.append(enemy) //...add enemy to hit fighter list
            }
        }
        //////////
        
        //////////BELOW LOOP IS FOR RARE SPIKESHIPS
        enumerateChildNodes(withName: "RareSpikeship") {node, _ in //move through all rares
            let enemy = node as! SKSpriteNode //alias to add to list
            self.enumerateChildNodes(withName: "laser") {node, _ in //move through all lasers
                let laser = node as! SKSpriteNode //alias to add to list
                if enemy.frame.intersects(laser.frame) { //if laser intersects enemy...
                    laserHitEnemy.append(enemy) //add enemy to hit list
                    lasersLanded.append(laser) //add laser to landed list
                }
                if  self.hitEnd1.frame.intersects(laser.frame) || self.hitEnd2.frame.intersects(laser.frame) || self.hitEnd3.frame.intersects(laser.frame) || self.hitEnd4.frame.intersects(laser.frame) { //if laser makes it to end of lane...
                    
                    laser.removeFromParent()
                    self.laserHitCounter = 0 //...reset hit counter
                    self.incrementIndicatorNode.text = "+5"
                }
            }
            if enemy.frame.intersects(hitbox) { //if enemy intersects fighter...
                enemyHitFighter.append(enemy) //...add enemy to hit fighter list
            }
        }
        //////////
        
        for enemy in laserHitEnemy { //move through enemies that laser has hit
            shotsHit = shotsHit + 1
            if enemy.name == "RareSpikeship" { //if enemy name is rare...
                enemy.texture = SKTexture(imageNamed: "CommonEnemy") //change texture to common
                enemy.name = "CommonSpikeship" //change name to common
                
                if laserHitCounter > 24 {
                    incrementIndicatorNode.text = "+25"
                }
                else if laserHitCounter > 19 {
                    incrementIndicatorNode.text = "+20"
                }
                else if laserHitCounter > 14 {
                    incrementIndicatorNode.text = "+15"
                }
                else if laserHitCounter > 9 { //code that tracks hit counters
                    incrementIndicatorNode.text = "+10"
                }
                else {
                    incrementIndicatorNode.text = "+5"
                }
                scoreLabelNode.text = String (score) //update score label
            }
            else { //else hit enemy is not rare...
                let explosion = SKEmitterNode(fileNamed: "ExplosionParticle")! //create explosion effect...
                explosion.position = enemy.position //...
                self.addChild(explosion) //...
                
                enemy.removeFromParent() //remove enemy from scene
                numOfEnemies = numOfEnemies - 1 //update...
                enemiesKilled = enemiesKilled + 1 //update...
                
                if laserHitCounter > 24 {
                    score = score + 25
                    incrementIndicatorNode.text = "+25"
                }
                else if laserHitCounter > 19 {
                    score = score + 20
                    incrementIndicatorNode.text = "+20"
                }
                else if laserHitCounter > 14 {
                    score = score + 15
                    incrementIndicatorNode.text = "+15"
                }
                else if laserHitCounter > 9 { //code that tracks hit counters
                    score = score + 10
                    //Draw +10 image from here
                    incrementIndicatorNode.text = "+10"
                }
                else {
                    score = score + 5
                    //Draw +5 image from here
                    incrementIndicatorNode.text = "+5"
                }
                scoreLabelNode.text = String (score) //update score label
                /* SOUND CODE FOR IMPLEMENTATION ONCE SOUNDS CREATED!
                if (defaults.bool(forKey: defaultsKeys.soundOff)) == false {
                    //play enemy explosion sound!
                }
                */
                self.run(SKAction.wait(forDuration: 1)) { //run explosion...
                    explosion.removeFromParent()
                }
            }
        }
        
        for laser in lasersLanded { //move through lasers in the lasers landed
            laserHitCounter = laserHitCounter + 1 //update...
            laser.removeFromParent() //remove laser from scene
        }
        
        for enemy in enemyHitFighter { //for each enemy that the fighter
            enemy.removeFromParent() //remove the enemy
            numOfEnemies = numOfEnemies - 1 //update...
            lives = lives - 1 //update...
            laserHitCounter = 0
            incrementIndicatorNode.text = "+5"
            
            let explosion = SKEmitterNode(fileNamed: "Fighter_ExplosionParticle")! //create and run explosion at fighter location
            explosion.position = fighter.position
            explosion.zPosition = 5
            self.addChild(explosion)
            /* SOUND CODE FOR IMPLEMENTATION ONCE SOUNDS CREATED!
            if (defaults.bool(forKey: defaultsKeys.soundOff)) == false {
                //play ship explosion sound!
            }
            */
            self.run(SKAction.wait(forDuration: 1)) {
                explosion.removeFromParent()
            }
            
            switch lives { //switch case that tracks player's lives
            case 2:
                life3Node.removeFromParent()
            case 1:
                life2Node.removeFromParent()
            case 0:
                life1Node.removeFromParent()
            case _ where lives < 0: //if fighter hit and has no lives left...
                let reveal = SKTransition.reveal(with: .up, duration: 1)
                let scene = GameOverScene(fileNamed: "GameOverScene")! //end game and switch to GameOverScene
                scene.scaleMode = .aspectFit
                scene.endScore = score //send data to GameOverScene
                scene.shotsFired = shotsFired //...
                scene.shotsHit = shotsHit //...

                self.view?.presentScene(scene, transition: reveal)
            default:
                print("Something went wrong! :(")
            }
        }
    }
    
    override func update(_ currentTime: TimeInterval) {
        // Called before each frame is rendered
        if (gamePaused) {
            scene?.view?.isPaused = true
        } else {
            checkCollisions()
            spawnVirus()
        }
    }
}
