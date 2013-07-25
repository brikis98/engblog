public_dir = "public"
source_dir = "source"
deploy_dir = "_deploy"
posts_dir = "_posts"
projects_dir = "_projects"
bios_dir = "_bios"
stash_dir = "_stash"

git_url = "git@github.com:thinkdeciduous/engblog.git"
git_branch = "gh-pages"
git_master = "master"
git_user = "thinkdeciduous"

dirs = [posts_dir, projects_dir, bios_dir]

desc "Install Jekyll and all it's dependencies."
task :install do
	begin
		require "jekyll"
		require "redcarpet"
		require "launchy"
		abort("Already installed all dependencies! Run \"rake deploy\" if you have not already to initialize the authoring environment")
	rescue LoadError
		puts "Installing Ruby dependencies..."
		system "gem install jekyll"
		system "gem install redcarpet"
		system "gem install launchy"
		system "mkdir public"
	end
end

desc "Setup the git-pages deployment area and initialize the repo for blog commits"
task :deploy do
	abort("Blog environment has already been deployed!") if File.directory?(deploy_dir)
	puts "Initializing authoring environment..."	
	system "mkdir #{deploy_dir}"
	cd "#{deploy_dir}" do
		# this all needs to be changed once I get the LinkedIn repo setup
		system "git init"
		system "git config user.name #{git_user}"
		system "git remote add origin #{git_url}"
		system "git pull origin"
		system "git checkout gh-pages"
	end
	FileUtils.mkdir("#{source_dir}/#{stash_dir}") unless File.exist?("#{source_dir}/#{stash_dir}")
	dirs.each do|dir|
		FileUtils.mkdir("#{source_dir}/#{stash_dir}/#{dir}") unless File.exist?("#{source_dir}/#{stash_dir}/#{dir}")
		FileUtils.cp Dir.glob("#{source_dir}/#{dir}/*"), "#{source_dir}/#{stash_dir}/#{dir}/"
	end
	puts "Building site (this could take a few minutes)..."
	system "jekyll build"
end

desc "Generate all static content with Jekyll and commit blog changes to github"
task :push, :override do |t, args|
	IO.popen("git pull origin master") { |out| abort("Local repo and master are out of sync please make sure your environment is up to date!") if out.gets.contains("conflict") }
	abort("Invalid option, use \"rake push --nocheck\" to override session checking") unless args.override == nil || args.override.downcase == "--nocheck"
	
	dirs.each do|dir|
		Dir.glob("#{source_dir}/#{dir}/*") do |file|
			if(File.file?("#{source_dir}/#{stash_dir}/#{dir}/#{File.basename(file)}") && FileUtils.compare_file(file, "#{source_dir}/#{stash_dir}/#{dir}/#{File.basename(file)}"))
					File.delete(file)
			else
					FileUtils.cp file, "#{source_dir}/#{stash_dir}/#{dir}/#{File.basename(file)}"
			end
		end
	end
	system "jekyll build"
	FileUtils.cp Dir.glob("#{source_dir}/#{stash_dir}/#{posts_dir}/*"), "#{source_dir}/#{posts_dir}/"
	FileUtils.cp Dir.glob("#{source_dir}/#{stash_dir}/#{projects_dir}/*"), "#{source_dir}/#{projects_dir}/"
	FileUtils.cp Dir.glob("#{source_dir}/#{stash_dir}/#{bios_dir}/*"), "#{source_dir}/#{bios_dir}/"
	cd "#{deploy_dir}" do
		system "git pull origin gh-pages"
	end
	#(Dir["#{deploy_dir}/*"]).each { |f| rm_rf(f) }
	cp_r "#{public_dir}/.", deploy_dir
	cd "#{deploy_dir}" do
		system "git add -A"
		message = "Site updated at #{Time.now.utc}"
		system "git commit -m \"#{message}\""
		system "git push origin #{git_branch}"
	end
	cd "#{source_dir}/#{posts_dir}" do
		system "git add -A"
		message = "Source upodated at #{Time.now.utc}"
		system "git commit -m \"#{message}\""
		system "git push origin #{git_master}"
	end
end

desc "Generate a temporary site and launch the Jekyll server"
task :preview do 
	require "launchy"
	cd "#{deploy_dir}" do
		Thread.new do
			sleep 4
			puts "Opening preview site in browser..."
			Launchy.open("http://localhost:4000")
		end
		puts "Generating preview site..."
		system "jekyll serve --watch"
		rm_rf "_site"
	end
end