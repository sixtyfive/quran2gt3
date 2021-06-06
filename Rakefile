require 'fileutils'
require 'vips' # for rendering text into PNGs
require 'chunky_png' # to create composed PNGs

DEFAULT_DPI = 300 # smaller one chosen based on image width in every given set
PAGE_X = {300 => 2480, 150 => 1240, 72 => 596}
PAGE_Y = {300 => 3508, 150 => 1754, 72 => 842}
FONTFILE = 'Scheherazade-Regular.ttf'
FONTNAME = 'Scheherazade Regular'

def print_usage
  puts "Usage: rake build"
  exit
end

task :default do
  print_usage
end

desc "rm -rf output/"
task :clean do
  cmd = "rm -rf output"
  puts cmd
  system(cmd)
end

desc "read quran.txt and split it into similarly-sized lines of text, saving 1 .gt.txt and 1 .png for each"
task :build do
  infile = 'quran.txt'

  inlines = []
  lines = []

  File.readlines(infile).each do |line|
    next unless line.match(/\d+\|\d/)
    inlines += line.split('|').last.strip.split(' ')
  end

  basmallah = inlines[0..3].join(' ')
  bsnippet = Vips::Image.text(basmallah, fontfile:FONTFILE, font:FONTNAME, dpi:DEFAULT_DPI)
  bsnippet.invert.write_to_file('basmallah.png') # just for debugging

  FileUtils.mkdir_p('output')
  line = []
  inlines.each do |word|
    if line.join(' ').size <= 3*basmallah.size
      line << word
    else
      lines << line.join(' ')
      line = []
    end
  end; print "Split #{infile} up into #{lines.size} lines of text. Writing PNG and TXT files now"

  lines.each_with_index do |line,i|
    basename = File.join('output', i.to_s.rjust(4,'0'))
    File.write(basename+'.gt.txt', line)
    line = Vips::Image.text(line, fontfile:FONTFILE, font:FONTNAME, dpi:DEFAULT_DPI)
    line.invert.write_to_file(basename+'.png')
    print('.')
  end; puts
end
