## autoload
当require 'erb'时，ruby会在$LOAD_PATH中去寻找目录下，是否有erb.rb, 另外LOAD_PATH一般来说只需要有最底层的目录就够了，不需要具体到文件.

## app目录下的子目录，都会自动在autoload_paths中