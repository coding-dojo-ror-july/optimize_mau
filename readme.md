cd desktop
cd vagrantbox

vagrant up
vagrant ssh
cd /vagrant/projects

rails new optimize

cd optimize

rails generate model Team name:string mascot:string stadium:string
rails generate model Player name:string team:references
rake db:migrate

rake db:seed

rails console

Hirb.enable

Player.all
Team.all

# 11 queries

players = Player.limit(10).order("RANDOM()")
players.each do |player|
  puts player.team.stadium
end

# 2 queries using includes

players = Player.includes(:team).limit(10).order("RANDOM()")
players.each do |player|
  puts player.team.stadium
end

# JOIN/WHERE

Player.includes(:team).where("teams.name = 'Los Angeles Lakers'").references(:team)

# 1. Retrive all players, then write a program that will loop through each player and display their team name, mascot and stadium. How many queries have we done?
# 1 query to retrieve all the players + 447 queries to get the team of each player = 448 total queries
players = Player.all
players.each do |player|
  team = player.team
  puts "Player: #{player.name}, Team: #{team.name}, Mascot: #{team.mascot}, Stadium: #{team.stadium}"
end

# 2. Retrive all players and their team by using .includes
# Active Record has eager loaded our association.
# When we call on the .team method on a player,
# we are not making another query but instead using the loaded assoication.
# We only executed 2 queries
players = Player.includes(:team)
players.each do |player|
  puts "Player: #{player.name}, Team: #{player.team.name}, Mascot: #{player.team.mascot}, Stadium: #{player.team.stadium}"
end

# 3. Retrieve all players from the 'Chicago Bulls' by using .includes
Player.includes(:team).where("teams.name = 'Chicago Bulls'").references(:team)

# 4. Retrieve all players along with the team name that play at the 'Staples Center'
Player.joins(:team).select('players.*', 'teams.name as franchise', 'teams.stadium as stadium').where("teams.stadium = 'Staples Center'")

# 5 Retrive all teams that have any player that start their name with the letter 'A' by both .includes and .joins
Team.includes(:players).where("players.name LIKE 'Z%'").references(:players)
Team.joins(:players).where("players.name LIKE 'Z%'")

# With .joins, we can also fetch more information. For example, we can retrieve the player's name as well
Team.joins(:players).where("players.name LIKE 'Z%'").select("teams.name as team_name", "teams.*", "players.*")

git init
git add .
git commit -m "first commit"
git remote add origin https://github.com/coding-dojo-ror-july/optimize_mau.git
git push -u origin master