---
layout: post
title: Volume Widget For AwesomeWM 3.4
categories:
  - lua
  - awesomewm
  - ubuntu
  - arch
  - linode

date: 11-05-2010
---

Ubuntu 9.10 was the distro I had been using for 10 months with AwesomeWM until my Arch Linux migration on last week. I started the migration by moving my development environment to the VPS located in Linode servers firstly, configuration of the hardware and desktop environment followed this step. During the configuration I noticed that most of the widgets in the AwesomeWM wiki doesn’t work with latest release. I took this situation as a sign  from the Gods of computer world to start learning Lua and read Programming In Lua book which is available to read online for free. A little training that took just 3-4 hours made me available to code a volume control widget for AwesomeWM. Here is the code:

{% highlight lua %}
------------------------------
-- Volume Widget For AwesomeWM 3.4
-- Azer Koculu <azerkoculu@gmail.com>
-- Wed Apr 7 01:34:35 UTC 2010
------------------------------
local widgetobj = widget({ type = 'textbox', name = 'volume_widget' })
local channel = "vmix0-outvol"

local function increase()
  awful.util.spawn("ossvol -i 3")
  update()
end

local function decrease()
  awful.util.spawn("ossvol -d 3")
  update()
end

local function update()
  local fd = io.popen("ossmix " .. channel)
  widgetobj.text = 'VOL' .. fd:read("*all"):match("(%d+%.%d+)")
  fd:close()
end

local function mute()
  awful.util.spawn("ossvol -t")
end

widgetobj:buttons(awful.util.table.join(
  awful.button({ }, '4', increase),
  awful.button({ }, '5', decrease),
  awful.button({ }, '1', mute)
))

--[[
globalkeys = awful.util.table.join(globalkeys,
awful.key({ }, "XF86AudioRaiseVolume", increase,
awful.key({ }, "XF86AudioLowerVolume", decrease,
awful.key({ }, "XF86AudioMute", mute)
)
]]--

local utimer = timer({ timeout=1 })
utimer:add_signal("timeout", update)
utimer:start()

update()

volume = {
  channel = channel,
  widget = widgetobj,
  increase = increase,
  decrease = decrease,
  mute = mute
}

return volume
{% endhighlight %}

I also coded an unnecessary wallpaper package without checking out man page of awsetbg, which provides randomizing already.

{% highlight lua %}
----------------------
-- Random Wallpaper Package For Awesome WM
-- Azer Koculu <azer@kodfabrik.com>
--
-- EXAMPLE USAGE
-- ------------
-- require "wallpaper"
-- theme.wallpaper_cmd = "awsetbg " .. wallpaper.pick( wallpaper.collect { "pics/wal1", "pics/wal2" } )
--
----------------------
local image_extensions = { jpg=true, png=true }

-- test whether given filename is an image
local function is_image(filename)
  return image_extensions[filename:match("%.(%w+)$")]
end

-- gather images from the passed directories
local function collect(dirs)
  local images = {}
  for i=1,table.getn(dirs),1 do
    local dir = dirs[i]
    for file in io.popen("ls "..dir):lines() do
      if is_image(file) then
        table.insert(images,dir .."/".. file)
      end
    end
  end
  return images
end

-- pick a random item from given table
local function pick(t)
  n = table.getn(t)
  if n > 0 then
    math.randomseed(os.time()+n)
    el = t[math.random( n )]
  end
  return el
end

-- declare exports
wallpaper = {
  extensions=image_extensions,
  collect=collect,
  pick=pick
}

return wallpaper
{% endhighlight %}
