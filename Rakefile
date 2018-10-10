require 'erb'

PORTS = (7000..7005).to_a

directory 'tmp'

rule %r{\./tmp/redis-(\d+).conf} => './redis.conf.erb' do |t|
  port = t.name[/\d+/].to_i
  erb = ERB.new(File.read(t.source))
  File.write t.name, erb.result(binding)
end

task up: PORTS.map { |port| "./tmp/redis-#{port}.conf" } do |t|
  cd './tmp' do
    PORTS.each do |port|
      sh "redis-server ./redis-#{port}.conf"
    end
  end
  servers = PORTS.map { |port| "127.0.0.1:#{port}" }
  sh "ruby ./redis-trib.rb create --replicas 1 #{servers.join ' '}"
end

task :down do
  PORTS.each do |port|
    pid = File.read("./tmp/pid#{port}").chomp
    sh "kill -KILL #{pid}"
  end
end

task default: :up
