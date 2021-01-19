# wild-clothing
FiveM Tøjshop, mere funktionel og mange flere funktionaliteter 

For at den loader tøjet, så skal du trigger der fra basen.

Du kan bruge denne funktion og erstatte den med din funktion nederst i din [vrp] > vrp > base.lua

FUNKTION:

RegisterServerEvent("vRPcli:playerSpawned")
AddEventHandler("vRPcli:playerSpawned", function()
  Debug.pbegin("playerSpawned")
  -- register user sources and then set first spawn to false
  local user_id = vRP.getUserId(source)
  local player = source
  if user_id ~= nil then
    vRP.user_sources[user_id] = source
    local tmp = vRP.getUserTmpTable(user_id)
    tmp.spawns = tmp.spawns+1
    local first_spawn = (tmp.spawns == 1)

    if first_spawn then
      -- first spawn, reference player
      -- send players to new player
      for k,v in pairs(vRP.user_sources) do
        vRPclient.addPlayer(source,{v})
      end
      -- send new player to all players
      vRPclient.addPlayer(-1,{source})
    end
   -- TriggerClientEvent("spawnselector:checkstats",player)
    -- set client tunnel delay at first spawn
    Tunnel.setDestDelay(player, config.load_delay)

    -- show loading
    vRPclient.setProgressBar(player,{"vRP:loading", "botright", "Indlæser din karakter...", 0,0,0, 100})

    SetTimeout(2000, function() -- trigger spawn event
      SetTimeout(config.load_duration*1000, function() -- set client delay to normal delay
        Tunnel.setDestDelay(player, config.global_delay)
        vRPclient.removeProgressBar(player,{"vRP:loading"})
        vRPclient.setProgressBar(player,{"vRP:loadingchar", "botright", "Loader skin...", 0,0,0, 100})
        Citizen.Wait(5000)
        TriggerClientEvent('raid_clothes:LoadYourClothes', player)
        vRPclient.removeProgressBar(player,{"vRP:loadingchar"})
        TriggerEvent("vRP:playerSpawn",user_id,player,first_spawn)
      end)
    end)
  end

  Debug.pend()
end)
