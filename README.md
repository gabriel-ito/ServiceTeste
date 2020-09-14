# ServiceTeste

package com.cartoes.api.services;

import static org.junit.Assert.assertNotNull;
import static org.junit.Assert.assertTrue;

import java.util.List;
import java.util.Optional;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.BDDMockito;
import org.mockito.Mockito;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.dao.DataIntegrityViolationException;
import org.springframework.test.context.ActiveProfiles;
import org.springframework.test.context.junit4.SpringRunner;

import com.cartoes.api.entities.Cartao;
import com.cartoes.api.entities.Transacao;
import com.cartoes.api.repositories.TransacaoRepository;
import com.cartoes.api.utils.ConsistenciaException;

@RunWith(SpringRunner.class)
@SpringBootTest
@ActiveProfiles("test")
public class TransacaoServiceTest {
	@MockBean
	private TransacaoRepository transacaoRepository;

	@Autowired
	private TransacaoService transacaoService;

	@Test
	public void testBuscarPorNumeroCartao() throws ConsistenciaException {

		BDDMockito.given(transacaoRepository.findByNumeroCartao(Mockito.anyString()));
		
		Optional<List<Transacao>> resultado = transacaoService.buscarPorNumeroCartao("");

		assertTrue(resultado.isPresent());

	}

	@Test(expected = ConsistenciaException.class)
	public void testBuscarPorNumeroCartaoNaoExistente() throws ConsistenciaException {

		BDDMockito.given(transacaoRepository.findByNumeroCartao(Mockito.anyString())).willReturn(null);

		transacaoService.buscarPorNumeroCartao("");

	}

	@Test
	public void testSalvarComSucesso() throws ConsistenciaException {

		BDDMockito.given(transacaoRepository.save(Mockito.any(Transacao.class))).willReturn(new Transacao());

		Transacao resultado = transacaoService.salvar(new Transacao());

		assertNotNull(resultado);

	}

	@Test(expected = ConsistenciaException.class)
	public void testSalvarNumeroCartaoNaoEncontrado() throws ConsistenciaException {

		BDDMockito.given(transacaoRepository.findByNumeroCartao(Mockito.anyString())).willReturn(Optional.empty());

		Transacao transacao = new Transacao();
		Cartao cartao = new Cartao();
		transacao.setCartao(cartao); 

		transacaoService.salvar(transacao);
	}

	@Test(expected = ConsistenciaException.class)
	public void testSalvarNumeroCartaoDuplicado() throws ConsistenciaException {

		BDDMockito.given(transacaoRepository.save(Mockito.any(Transacao.class)))
				.willThrow(new DataIntegrityViolationException(""));

		transacaoService.salvar(new Transacao());
	}

}
