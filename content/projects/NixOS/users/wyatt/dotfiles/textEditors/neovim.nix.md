```nix{ config, lib, pkgs, ... }:
	let
		perlPkgs = with pkgs.perl532Packages; [
			Appcpanminus
			ArchiveZip
		];
	in {
		home.packages = with pkgs; [
			perl
			perl534Packages.Appcpanminus
                        perl534Packages.ArchiveZip
                        tree-sitter
                        languagetool
		];
		programs.neovim = {
			enable = true;
			#package = pkgs.neovim-nightly/*.neovim-nightly*//*.neovim*/;
			withNodeJs = true;
			withPython3 = true;
			withRuby = true;
			coc = {
                          enable = true;
                          settings = {
                              languageserver = {
                                nix = {
                                  command = "rnix-lsp";
                                  filetypes = [
                                    "nix"
                                  ];
                                };
                              };
                            };
                          };
			vimAlias = true;
			plugins = with pkgs.vimPlugins; [
				coc-nvim
                                coc-vimtex
                                coc-solargraph
                                vimtex
                                nvim-base16
				vim-nix
                                undotree
                                nvim-treesitter
                                ale
                                LanguageTool-nvim
                                vim-hexokinase
			];
			extraPackages = with pkgs;  [
				neovim-remote
                              ];
                        extraConfig = ''
                                lua <<EOF
                                  require'nvim-treesitter.configs'.setup {
                                    ensure_installed = "maintained",
                                    highlight = {
                                      enable = true,
                                      disable = {},
                                    },
                                  }
EOF
                                highlight TrailingWhitespace ctermbg=red guibg=red
                                call matchadd("TrailingWhitespace", '\v\s+$')
                                set spell
                                colorscheme base16-dracula
                                set spelllang=en
                                let g:languagetool_server_jar = '${pkgs.languagetool}/share/languagetool-server.jar'
                                let g:Hexokinase_highlighters = [ 'virtual' ]
			'';
		};
}

```
